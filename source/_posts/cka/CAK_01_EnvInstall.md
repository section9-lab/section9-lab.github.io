---
title: K8s环境安装
date: 2024-11-12
category:
  - CKA
tag:
  - k8s
sticky: false
---

# K8s环境安装



## 1、操作系统准备
### 1.1、安装虚拟机软件
> 我的电脑是一台16G的macbook-pro，需要在电脑上安装一款虚拟机软件，然后在虚拟机上运行多台Linux服务器。

| 虚拟机软件     | 支持的操作系统 | 说明       |
| -----------  | -----------  |-----------|
| vmware       | mac win linux | 提供收费和免费版  |
| VirtualBox   | mac win linux | 个人和企业版均免费 |
| Hyper-V      | win            | Windows子系统(WSL)安装Linux  |
| Multipass    | mac win linux |  轻量、快速、支持命令行 |
| Parallels Desktop | mac       |  Mac下使用Windows体验较好，收费 |
| UTM          | mac            | M系列花片模批x86架构，基于QEMU  |
| QEMU         | mac win linux |  模拟不同指令集架构 |

相比于vmware和VirtualBox，Multipass更加的轻量快捷

安装`multipass`
```zsh
brew install multipass
```
卸载`multipass`
```zsh
$ brew uninstall multipass
```
卸载并删除数据
```zsh
$ brew uninstall --zap multipass
```
查看软件版本
```zsh
multipass version
multipass   1.14.1+mac
multipassd  1.14.1+mac
```
multipass常用命令
```
# 创建一个名字叫做k3s的虚拟机
multipass launch --name k3s

# 在虚拟机中执行命令
multipass exec k3s -- ls -l

# 进入虚拟机并执行shell
multipass shell k3s

# 查看虚拟机的信息
multipass info k3s

# 停止虚拟机
multipass stop k3s

# 启动虚拟机
multipass start k3s

# 删除虚拟机
multipass delete k3s

# 清理虚拟机
multipass purge

# 挂载目录（将本地的~/kubernetes/master目录挂载到虚拟机中的~/master目录）
multipass mount ~/kubernetes/master master:~/master
```

### 1.2、在虚拟机上创建操作系统镜像

节点规划

| node_name  |cpu|mem|disk| system        | ip       |
| ---------- |---|---|----|-------------- | -------- |
| k8s-master | 2 |2G |10G |ubuntu22.04LTS | 10.0.0.1 |
| k8s-work01 | 2 |2G |10G |ubuntu22.04LTS | 10.0.0.2 |
| k8s-work02 | 2 |2G |10G |ubuntu22.04LTS | 10.0.0.3 |

查看有那些可用的镜像
```zsh
$ multipass find
Image   Aliases   Version    Description
core    core16    20200818   Ubuntu Core 16
core20            20230119   Ubuntu Core 20
core22            20230717   Ubuntu Core 22
core24            20240603   Ubuntu Core 24
20.04   focal     20241112   Ubuntu 20.04 LTS
22.04   jammy     20241002   Ubuntu 22.04 LTS
24.04   noble,lts 20241106   Ubuntu 24.04 LTS
```
创建3台服务器
```
multipass launch --name k8s-master --cpus 2 --memory 2G --disk 10G jammy
multipass launch --name k8s-work01 --cpus 2 --memory 2G --disk 10G jammy
multipass launch --name k8s-work02 --cpus 2 --memory 2G --disk 10G jammy
```
查看是否创建了3个虚拟机(创建后默认是启动的)
```
$ multipass list
Name                    State             IPv4             Image
k8s-master              Running           192.168.64.3     Ubuntu 22.04 LTS
k8s-work01              Running           192.168.64.4     Ubuntu 22.04 LTS
k8s-work02              Running           192.168.64.5     Ubuntu 22.04 LTS
```

### 1.3、镜像操作系统的配置设定

- 密码配置

设置密码（镜像默认没有密码）
sudo passwd ubuntu

- 设置静态ip
```
#备份配置
root@k8s-master:~# mv /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.yaml.bak

#写入新的配置
root@k8s-master:~# cat << EOF > /etc/netplan/50-cloud-init.yaml
# This is the network config written by 'subiquity'
network:
  version: 2
  renderer: networkd
  ethernets:
    ens3:
      dhcp4: no
      addresses:
        - 192.168.64.3/24
      routes:
        - to: default
          via: 192.168.64.1
      nameservers:
        addresses:
          - 223.6.6.6
          - 114.114.114.114
          - 8.8.8.8
EOF

# 应用配置文件
netplan apply
```

- 关闭防火墙和selinux

```
# 查看防火墙状态
sudo ufw status
# 关闭防火墙
sudo ufw disable
# 永久关闭防火墙
sudo systemctl disable ufw

#关闭 selinux（22.04并不是默认安装的）
sudo setenforce 0
# 永久关闭 selinux 打开配置文件 ,将 SELINUX=enforcing 修改为 SELINUX=disabled
sudo vi /etc/selinux/config
```

- 关闭swap分区

```
#查看
sudo swapon --show
#禁用
sudo swapoff /dev/sdaX
#永久禁用
从 /etc/fstab 文件中删除与交换分区相关的行。
```

- 设置hostname

```
hostnamectl set-hostname k8s-master
hostnamectl set-hostname k8s-work01
hostnamectl set-hostname k8s-work02
```

- host解析主机配置

echo '''
192.168.64.3 k8s-master
192.168.64.4 k8s-work01
192.168.64.5 k8s-work02
''' >> /etc/hosts

- 设置时区

```
# 查看当前时区
timedatectl status

# 设置时区为上海
sudo timedatectl set-timezone Asia/Shanghai

# 查看时区是否设置成功
timedatectl status
```

- 配置内核转发和网桥过滤

```
#配置文件地址写入
cat > /etc/modules-load.d/k8s.conf << EOF
overlay
br_netfilter
EOF

#加载配置
modprobe overlay && modprobe br_netfilter

# 查看是否加载
lsmod |grep overlay
lsmod |grep br_netfilter



cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

#让配置生效：
sysctl -p /etc/sysctl.d/k8s.conf
#查看是否加载生效
lsmod |grep br_netfilter
```

- 安装ipset和ipvsadm

```
apt install ipset ipvsadm -y

#配置 ipvsadm 模块加载方式
cat > /etc/modules-load.d/ipvs.conf << EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack
EOF

#写成一个手动启动脚本文件
cat << EOF | tee ipvs.sh
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack
EOF
```

### 1.4、安装容器
```
#containerd下载
wget https://github.com/containerd/containerd/releases/download/v1.7.23/cri-containerd-1.7.23-linux-amd64.tar.gz

#解压并查看
tar xf cri-containerd-1.7.23-linux-amd64.tar.gz -C /
which containerd
#containerd配置文件生成并修改
#创建文件
mkdir /etc/containerd
#生成配置文件：
containerd config default > /etc/containerd/config.toml

#修改配置文件
#修改配置文件将pause:3.8改为3.9
cat /etc/containerd/config.toml |grep "pause:"
    sandbox_image = "registry.k8s.io/pause:3.8"
#将SystemdCgroup = false改为true

#启动并设置开机自启
systemctl enable --now containerd

#查看版本：
containerd --version
```

## 2、安装k8s

### 2.1、安装相关软件

- 2.1.1、设置镜像源

```
#创建目录
sudo mkdir -p /etc/apt/keyrings/
#下载密钥
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
#添加kubernetes apt仓库
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

- 2.1.2、安装`kubelet` `kubeadm` `kubectl`

```
apt clean && rm -rf /var/lib/apt/lists/* && apt update

#如果遇到时间不同步可以手动指定时间
date -s "2024-11-19 18:29:40"

#查看软件列表：
root@k8s-work01:~# apt-cache madison kubeadm
   kubeadm | 1.31.2-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages
   kubeadm | 1.31.1-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages
   kubeadm | 1.31.0-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages

#安装指定版本：
sudo apt-get install -y kubelet=1.31.0-1.1 kubeadm=1.31.0-1.1 kubectl=1.31.0-1.1

#锁定版本，防止后期自动更新。
sudo apt-mark hold kubelet kubeadm kubectl
```

### 2.2、集群初始化

生成配置文件：
kubeadm config print init-defaults > kubeadm-config.yaml

修改配置文件：
vim kubeadm-config.yaml
```
1、修改master在集群中的名称
nodeRegistration:
  name: k8s-master #主节点名

2、添加master主机节点的IP
localAPIEndpoint:
  advertiseAddress: 192.168.64.3  # master 主机IP

3、添加pod集群的IP段
networking:
  podSubnet: 10.244.0.0/16

4、添加镜像仓库地址（根据需要设置）
imageRepository: registry.aliyuncs.com/google_containers

5、cgroupDriver 被设置为 systemd
---
apiVersion: kubelet.config.k8s.io/v1beta4
kind: KubeletConfiguration
cgroupDriver: systemd
```

- 初始化镜像
```
#查看镜像
root@k8s-master:~# kubeadm config images list --config kubeadm-config.yaml
registry.k8s.io/kube-apiserver:v1.31.0
registry.k8s.io/kube-controller-manager:v1.31.0
registry.k8s.io/kube-scheduler:v1.31.0
registry.k8s.io/kube-proxy:v1.31.0
registry.k8s.io/coredns/coredns:v1.11.1
registry.k8s.io/pause:3.10
registry.k8s.io/etcd:3.5.15-0

#下载镜像
root@k8s-master:~# kubeadm config images pull --config kubeadm-config.yaml
[config/images] Pulled registry.k8s.io/kube-apiserver:v1.31.0
[config/images] Pulled registry.k8s.io/kube-controller-manager:v1.31.0
[config/images] Pulled registry.k8s.io/kube-scheduler:v1.31.0
[config/images] Pulled registry.k8s.io/kube-proxy:v1.31.0
[config/images] Pulled registry.k8s.io/coredns/coredns:v1.11.1
[config/images] Pulled registry.k8s.io/pause:3.10
[config/images] Pulled registry.k8s.io/etcd:3.5.15-0

#初始化集群
kubeadm init --config kubeadm-config.yaml
.......
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.64.3:6443 --token abcdef.0123456789abcdef \
	--discovery-token-ca-cert-hash sha256:69a0ee0d71ae9ba0438d7c92988a3057d617ea13917b7718df0f4fb142c7fceb
```

根据上面的提示
```
1、
root@k8s-master:~# mkdir -p $HOME/.kube && sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config && sudo chown $(id -u):$(id -g) $HOME/.kube/config

2、
root@k8s-master:~# export KUBECONFIG=/etc/kubernetes/admin.conf

3、
root@k8s-master:~# kubectl get nodes
NAME   STATUS     ROLES           AGE     VERSION
node   NotReady   control-plane   3m56s   v1.31.0

4、此时在其他两个work节点上执行加入集群的操作
kubeadm join 192.168.64.3:6443 --token abcdef.0123456789abcdef \
	--discovery-token-ca-cert-hash sha256:69a0ee0d71ae9ba0438d7c92988a3057d617ea13917b7718df0f4fb142c7fceb

5、
root@k8s-master:~# kubectl get nodes
NAME         STATUS     ROLES           AGE     VERSION
k8s-work01   NotReady   <none>          25s     v1.31.0
k8s-work02   NotReady   <none>          17s     v1.31.0
node         NotReady   control-plane   4m42s   v1.31.0
root@k8s-master:~#
```

### 2.3、安装网络插件Calico(只在master执行)
Calico是一个网络插件，提供高性能网络功能和网络安全策略

|特性  	 |Calico 	 |Flannel 	|Cilium |
|-------|---------|----------|-------|
|数据路径| 路由+eBPF|Overlay网络|	eBPF  |
|网络策略|支持    	|不支持	   | 支持   |
|性能    |高      	|较低       |	高    |
|复杂性	|中等   	 |简单       |较复杂  |
|扩展性	|高      	|较低	      |高     |

![](/images/k8s-network.png)

```
#创建配置文件 1
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.2/manifests/tigera-operator.yaml

#查看是否运行：
root@k8s-master:~# kubectl get pods -n tigera-operator
NAME                              READY   STATUS              RESTARTS   AGE
tigera-operator-89c775547-xmrnt   0/1     ContainerCreating   0          20s

wget https://raw.githubusercontent.com/projectcalico/calico/v3.28.2/manifests/custom-resources.yaml
# 注我们在kubeadm-config.yaml文件中添加的pod网段是10.244.0.0/16所以我们要修改为一样的
vim custom-resources.yaml

#应用配置
kubectl create -f custom-resources.yaml

#查看命名空间
root@k8s-master:~# kubectl get ns
NAME              STATUS   AGE
calico-system     Active   5s
default           Active   9m41s
kube-node-lease   Active   9m40s
kube-public       Active   9m43s
kube-system       Active   9m46s
tigera-operator   Active   2m24s

# 查看命名空间中运行的pods 如果没有全部起来稍微等一下，应该是在创建中，如果网络慢可能要半个小时。
root@k8s-master:~# kubectl get pods -n calico-system
NAME                                       READY   STATUS              RESTARTS   AGE
calico-kube-controllers-85459c57fb-pl965   0/1     Pending             0          34s
calico-node-86pq4                          0/1     Init:0/2            0          38s
calico-node-9n9bf                          0/1     Init:0/2            0          38s
calico-node-mh8cx                          0/1     Init:1/2            0          38s
calico-typha-66c7594bc7-dbzjk              0/1     ContainerCreating   0          32s
calico-typha-66c7594bc7-rv6ph              0/1     ContainerCreating   0          40s
csi-node-driver-7nv5x                      0/2     ContainerCreating   0          36s
csi-node-driver-bf87x                      0/2     ContainerCreating   0          37s
csi-node-driver-w9mfl                      0/2     ContainerCreating   0          37s

#查看集群状态：
kubectl get nodes
NAME         STATUS     ROLES           AGE     VERSION
k8s-work01   NotReady   <none>          4m37s   v1.31.0
k8s-work02   NotReady   <none>          4m31s   v1.31.0
node         NotReady   control-plane   10m     v1.31.0
```

## 3、测试k8s集群
### 3.1、安装nginx测试集群可用性
```
root@k8s-master:~# cat nginx.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginxweb
spec:
  selector:
    matchLabels:
      app: nginxweb1
  replicas: 2
  template:
    metadata:
      labels:
        app: nginxweb1
    spec:
      containers:
      - name: nginxwebc
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: nginxweb-service
spec:
  externalTrafficPolicy: Cluster
  selector:
    app: nginxweb1
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080
  type: NodePort
```

启动创建容器
kubectl apply -f nginx.yaml

查看是否创建成功
```
root@k8s-master:~# kubectl get deployment
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
nginxweb   1/2     2            1           32m


root@k8s-master:~# kubectl get pods
NAME                       READY   STATUS              RESTARTS   AGE
nginxweb-b4ccbf5dc-f684w   1/1     Running             0          32m
nginxweb-b4ccbf5dc-hbpxs   0/1     ContainerCreating   0          32m


root@k8s-master:~# kubectl get svc
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP   10.96.0.1        <none>        443/TCP        40h
nginxweb-service   NodePort    10.104.125.155   <none>        80:30080/TCP   48m
```

### 3.2、网络访问nginx
network test
```
╭─test@MacBook-Pro ~
╰─$ curl 192.168.64.3:30080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

╭─test@MacBook-Pro ~
╰─$ curl 192.168.64.4:30080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

╭─test@MacBook-Pro ~
╰─$ curl 192.168.64.5:30080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
