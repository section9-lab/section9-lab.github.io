---
title: Kubernetes
date: 2023-11-24
category:
   - Linux
tag:
  - tools
sticky: false
---

# Kubernetes
## 1、部署架构的发展
![](/images/runtime.svg)
- 1、统部署方式：
> 应用直接部署于物理机无法控制资源分配,一旦出现Bug,可能导致机器资源被单个应用占用,其它应用无法正常运行,无法实现应用隔离。

- 2、虚拟机部署
> 在单个物理机上运行多个虚拟机，每个虚拟机都是完整独立的系统，性能损耗大。

- 3、容器部署
> 所有容器共享主机的系统，轻量级的虚拟机，性能损耗小，资源隔离，CPU和内存可按需分配

## 2、既然有了Docker那k8s解决了什么问题？
- 单机Docker很好用，但是服务器上百台、上千台时，每次加机器、软件更新、版本回滚，都会变得非常麻烦; 如果容器发生故障，需要手动启动另一个容器
- Kubernetes 提供集中式的管理集群机器和应用，加机器、版本升级、版本回滚、不停机的灰度更新、确保高可用、高性能、高扩展。

## 3、Kubernetes 提供：
- 服务发现和负载均衡
> Kubernetes 可以使用 DNS 名称或自己的 IP 地址来暴露容器。 如果进入容器的流量很大， Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。
- 存储编排
> Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。
- 自动部署和回滚
> Kubernetes可以 declaratively 管理容器部署的预期状态,并以受控速率将实际状态调节为期望状态,实现自动弹性扩缩容器实例。
- 自动完成装箱计算
>  Kubernetes 是容器集群管理系统,可在集群节点上调度运行容器化应用,并根据指定的 CPU 和内存资源需求进行优化调度,以达到资源的最优利用。
- 自我修复
> Kubernetes 将重新启动失败的容器、替换容器、杀死不响应用户定义的运行状况检查的容器， 并且在准备好服务之前不将其通告给客户端。
- 密钥与配置管
> Kubernetes可以安全管理密码、令牌、SSH密钥等敏感信息,实现应用配置和密钥的更新部署,无需重建镜像和在配置文件中暴露密钥。
- 批处理执行 
>除了服务外，Kubernetes 还可以管理你的批处理和 CI（持续集成）工作负载，如有需要，可以替换失败的容器。
- 水平扩缩 
>使用简单的命令、用户界面或根据 CPU 使用率自动对你的应用进行扩缩。
- IPv4/IPv6 双栈 
> 为 Pod（容器组）和 Service（服务）分配 IPv4 和 IPv6 地址。
- 为可扩展性设计 
> 在不改变上游源代码的情况下为你的 Kubernetes 集群添加功能。

## 4、k8s架构
Kubernetes集群由一组工作节点组成,这些节点上运行容器化应用程序。集群包含至少一个工作节点和控制平面,控制平面负责管理节点上运行的Pod。生产环境下控制平面跨多节点运行,并配合多工作节点实现容错和高可用。
![k8s架构图](/images/components-of-kubernetes.svg)

### 4.1、控制平面组件
控制平面组件会为集群做出全局决策，比如资源的调度。 以及检测和响应集群事件，例如当不满足部署的 replicas 字段时， 要启动新的 pod。

#### 4.1.1、kube-apiserver
Kubernetes的API服务器公开了Kubernetes API用以处理请求,是控制平面的前端,其主要实现是kube-apiserver,支持通过运行多个实例实现水平扩展和流量负载均衡。
#### 4.1.2、etcd
一致且高可用的键值存储，用作 Kubernetes 所有集群数据的后台数据库。

#### 4.1.3、kube-scheduler
kube-scheduler是Kubernetes控制平面的组件,负责监视新创建的未调度Pod,并根据资源需求、策略限制、亲和性规范等多维因素选择最优节点去运行Pod。

#### 4.1.4、kube-controller-manager
kube-controller-manager 是控制平面的组件， 负责运行控制器进程。

从逻辑上讲， 每个控制器都是一个单独的进程， 但是为了降低复杂性，它们都被编译到同一个可执行文件，并在同一个进程中运行。

有许多不同类型的控制器。以下是一些例子：
- 节点控制器（Node Controller）：负责在节点出现故障时进行通知和响应
- 任务控制器（Job Controller）：监测代表一次性任务的 Job 对象，然后创建 Pods 来运行这些任务直至完成
- 端点分片控制器（EndpointSlice controller）：填充端点分片（EndpointSlice）对象（以提供 Service 和 Pod 之间的链接）。
- 服务账号控制器（ServiceAccount controller）：为新的命名空间创建默认的服务账号（ServiceAccount）。

#### 4.1.5、cloud-controller-manager
云控制器管理器是控制平面的组件,用来将Kubernetes集群连接到特定云平台并实现云平台相关的控制逻辑,它分离了云平台交互组件与集群内交互组件,对于本地环境部署的集群是可选的。
云控制器管理器组合了多个逻辑上独立的控制回路,可以水平扩展提升性能和容错能力。

下面的控制器都包含对云平台驱动的依赖：
- 节点控制器（Node Controller）：用于在节点终止响应后检查云提供商以确定节点是否已被删除
- 路由控制器（Route Controller）：用于在底层云基础架构中设置路由
- 服务控制器（Service Controller）：用于创建、更新和删除云提供商负载均衡器

### 4.2、Node组件
节点组件会在每个节点上运行，负责维护运行的 Pod 并提供 Kubernetes 运行环境。

#### 4.2.1、kubelet
kubelet 会在集群中每个节点（node）上运行。 它保证容器（containers）都运行在 Pod 中。

kubelet 接收一组通过各类机制提供给它的 PodSpec，确保这些 PodSpec 中描述的容器处于运行状态且健康。 kubelet 不会管理不是由 Kubernetes 创建的容器。

#### 4.2.2、kube-proxy
kube-proxy 是集群中每个节点（node）上所运行的网络代理， 实现 Kubernetes 服务（Service） 概念的一部分。

kube-proxy 维护节点上的一些网络规则， 这些网络规则会允许从集群内部或外部的网络会话与 Pod 进行网络通信。

如果操作系统提供了可用的数据包过滤层，则 kube-proxy 会通过它来实现网络规则。 否则，kube-proxy 仅做流量转发。

#### 4.2.3、容器运行时（Container Runtime）
这个基础组件使 Kubernetes 能够有效运行容器。 它负责管理 Kubernetes 环境中容器的执行和生命周期。

Kubernetes 支持许多容器运行环境，例如 containerd、 CRI-O 以及 Kubernetes CRI (容器运行环境接口) 的其他任何实现。

## 5、安装
`minikube` 是一个工具， 能让你在本地运行 Kubernetes。

::: code-tabs#shell

@tab debian

```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```

@tab rpm

```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
sudo rpm -Uvh minikube-latest.x86_64.rpm
```

:::


## 6、使用

### 6.1、minikube指令

mac安装minikube
```
brew install minikube
```
通过运行以下命令启动集群：
```shell
minikube start
```
版本升级
```
minikube update-check

CurrentVersion: v1.34.0
LatestVersion: v1.34.0
```
访问在 minikube 集群中运行的 Kubernetes 仪表板：
```shell
minikube dashboard
```
Minikube 使在浏览器中打开这个公开的端点变得容易：
```shell
minikube service hello-minikube
```
升级集群：
```shell
minikube start --kubernetes-version=latest
```
启动第二个本地集群（注意：如果 minikube 使用裸机/无驱动程序，这将不起作用）：
```shell
minikube start -p cluster2
```
停止本地群集：
```shell
minikube stop
```
删除本地集群：
```shell
minikube delete
```
删除所有本地集群和配置文件
```shell
minikube delete --all
```

### 6.2、k8s指令
启动服务器：kubectl
```shell
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
```
将服务公开为 NodePort
```shell
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

获取节点信息
```
kubectl get nodes     

NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   66m   v1.31.0
```

获取集群的命名空间
```
kubectl get namespaces

NAME                   STATUS   AGE
default                Active   65m
kube-node-lease        Active   65m
kube-public            Active   65m
kube-system            Active   65m
kubernetes-dashboard   Active   21m
```

获取pod运行信息(pod中也运行着k8s自身所需要的服务)
```
kubectl get pods -A
NAMESPACE              NAME                                READY   STATUS    RESTARTS      AGE
kube-system            coredns-6f6b679f8f-2zsb4            1/1     Running   0             67m
kube-system            etcd-minikube                       1/1     Running   0             67m
kube-system            kube-apiserver-minikube             1/1     Running   0             67m
kube-system            kube-controller-manager-minikube    1/1     Running   0             67m
kube-system            kube-proxy-q64vb                    1/1     Running   0             67m
kube-system            kube-scheduler-minikube             1/1     Running   0             67m
kube-system            storage-provisioner                 1/1     Running   1 (67m ago)   67m
kubernetes-dashboard   dashboard-metrics-scraper-cb4-24k   1/1     Running   0             24m
kubernetes-dashboard   kubernetes-dashboard-695-rwvnn      1/1     Running   0             24m
```

获取服务信息
```
kubectl get services -A
NAMESPACE              NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)           AGE
default                kubernetes                  ClusterIP   10.96.0.1      <none>        443/TCP           71m
kube-system            kube-dns                    ClusterIP   10.96.0.10     <none>        53/UDP,9153/TCP   71m
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.99.160.10   <none>        8000/TCP          27m
kubernetes-dashboard   kubernetes-dashboard        ClusterIP   10.108.78.40   <none>        80/TCP            27m
╭─localhost@MacBook-Pro ~ 
```

通过YAML创建命名空间

vim namespace.yaml 
```
---
apiVersion: v1
kind: Namespace
metadata:
  name: development
```
```
╰─$ kubectl apply -f namespace.yaml 
namespace/development created
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get namespaces
NAME                   STATUS   AGE
default                Active   24h
development            Active   23s
kube-node-lease        Active   24h
kube-public            Active   24h
kube-system            Active   24h
kubernetes-dashboard   Active   23h
```


增加一个生产环境命名空间
vim namespace.yaml 
```
---
apiVersion: v1
kind: Namespace
metadata:
  name: development
---
apiVersion: v1
kind: Namespace
metadata:
  name: production
```
```
kubectl apply -f namespace.yaml
namespace/development unchanged
namespace/production created

╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get namespaces         
NAME                   STATUS   AGE
default                Active   24h
development            Active   3m7s
kube-node-lease        Active   24h
kube-public            Active   24h
kube-system            Active   24h
kubernetes-dashboard   Active   23h
production             Active   6s
```

通过YAML删除命名空间
```
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl delete -f namespace.yaml 
namespace "development" deleted
namespace "production" deleted
```
```
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get namespaces          
NAME                   STATUS   AGE
default                Active   24h
kube-node-lease        Active   24h
kube-public            Active   24h
kube-system            Active   24h
kubernetes-dashboard   Active   23h
```

部署应用程序

vim deployment.yml
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pod-info-deployment
  namespace: development
  labels:
    app: pod-info
spec:
  replicas: 3
  selector:
    matchLabels:
      app: pod-info
  template:
    metadata:
      labels:
        app: pod-info
    spec:
      containers:
      - name: pod-info-container
        image: kimschles/pod-info-app:latest
        ports:
        - containerPort: 3000
        env:
          - name: POD_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: POD_NAMESPACE
            valueFrom:
              fieldRef:
                fieldPath: metadata.namespace
          - name: POD_IP
            valueFrom:
              fieldRef:
                fieldPath: status.podIP
```
根据YAML文件创建
```
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl apply -f deployment.yaml                                         
deployment.apps/pod-info-deployment created

```
查看创建后的状态
```
# 查看指定命名空间下的控制器信息
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get deployments -n development
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
pod-info-deployment   3/3     3            3           3m43s

#查看development命名空间下的pod
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get pods -n development       
NAME                                   READY   STATUS    RESTARTS   AGE
pod-info-deployment-7b697c564d-w4mlt   1/1     Running   0          3m39s
pod-info-deployment-7b697c564d-wrgsz   1/1     Running   0          3m39s
pod-info-deployment-7b697c564d-z4rk6   1/1     Running   0          3m39s
```

测试k8s能否故障恢复
测试方法：将development服务中的3个pod删除1个，之后集群会跟进配置的3副本再启动一个新node。
操作如下：
```
#当前运行的三个node 时间也一样
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get pods -n development            
NAME                                   READY   STATUS    RESTARTS   AGE
pod-info-deployment-7b697c564d-w4mlt   1/1     Running   0          24m
pod-info-deployment-7b697c564d-wrgsz   1/1     Running   0          24m
pod-info-deployment-7b697c564d-z4rk6   1/1     Running   0          24m
#删除1个node
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl delete pod pod-info-deployment-7b697c564d-w4mlt -n development
pod "pod-info-deployment-7b697c564d-w4mlt" deleted
^C% 
#再次查看其中1个node name变了 而且运行时间短
╭─localhost@MacBook-Pro ~ 
╰─$ kubectl get pods -n       development
NAME                                   READY   STATUS    RESTARTS   AGE
pod-info-deployment-7b697c564d-6fg5x   1/1     Running   0          34s
pod-info-deployment-7b697c564d-wrgsz   1/1     Running   0          26m
pod-info-deployment-7b697c564d-z4rk6   1/1     Running   0          26m

```

通过事件日志查看pod运行情况
1、获取命名空间的pod服务信息
```
kubectl get pods -n development
NAME                                   READY   STATUS    RESTARTS   AGE
pod-info-deployment-7b697c564d-6fg5x   1/1     Running   0          5d21h
pod-info-deployment-7b697c564d-wrgsz   1/1     Running   0          5d21h
pod-info-deployment-7b697c564d-z4rk6   1/1     Running   0          5d21h
```
2、通过 describe pod 查看某个命名空间显具体pod的详细信息
```
kubectl describe pod pod-info-deployment-7b697c564d-6fg5x -n development
Name:             pod-info-deployment-7b697c564d-6fg5x
Namespace:        development
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sat, 26 Oct 2024 01:32:53 +0800
Labels:           app=pod-info
                  pod-template-hash=7b697c564d
Annotations:      <none>
Status:           Running
IP:               10.244.0.8
IPs:
  IP:           10.244.0.8
Controlled By:  ReplicaSet/pod-info-deployment-7b697c564d
Containers:
  pod-info-container:
    Container ID:   docker://4a7845e78feac55c79e9eb8f2f993655e2e0573e3fd2a06e87d9b8474f15c090
    Image:          kimschles/pod-info-app:latest
    Image ID:       docker-pullable://kimschles/pod-info-app@sha256:fa4f33bc2301bb242bdd078ac206d0e379dfed2e225d46a6952ff444ae6f4a7a
    Port:           3000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 26 Oct 2024 01:33:06 +0800
    Ready:          True
    Restart Count:  0
    Environment:
      POD_NAME:       pod-info-deployment-7b697c564d-6fg5x (v1:metadata.name)
      POD_NAMESPACE:  development (v1:metadata.namespace)
      POD_IP:          (v1:status.podIP)
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-pdtg2 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-pdtg2:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>

```
使用BusyBox检查应用程序
1、创建一个运行BusyBox的pod，默认命名空间，1个副本就行
vim busybox.yaml
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox
  namespace: default
  labels:
    app: busybox
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
      - name: busybox-container
        image: busybox:latest
        # Keep the container running
        command: [ "/bin/sh", "-c", "--" ]
        args: [ "while true; do sleep 30; done;" ]
        resources:
          requests:
            cpu: 30m
            memory: 64Mi
          limits:
            cpu: 100m
            memory: 128Mi
```
2、创建BusyBox pod
```
kubectl apply -f busybox.yaml 
deployment.apps/busybox created
```
3、查看是否启动
```
kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
busybox-574654f4cb-fldrp   1/1     Running   0          59s
```
4、获取pod附加信息和K8s的IP地址
```
kubectl get pods -n development -o wide                                                     
NAME                                   READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
pod-info-deployment-7b697c564d-6fg5x   1/1     Running   0          5d22h   10.244.0.8   minikube   <none>           <none>
pod-info-deployment-7b697c564d-wrgsz   1/1     Running   0          5d23h   10.244.0.5   minikube   <none>           <none>
pod-info-deployment-7b697c564d-z4rk6   1/1     Running   0          5d23h   10.244.0.6   minikube   <none>           <none>

```
5、进入pod内运行命令
```
kubectl exec -it busybox-574654f4cb-fldrp -- /bin/sh
/ # 
```
6、测试k8s内的pod网络是否可以连通
```
/ # wget 10.244.0.8:3000
Connecting to 10.244.0.8:3000 (10.244.0.8:3000)
saving to 'index.html'
index.html           100% |******************************************************************************|   103  0:00:00 ETA
'index.html' saved
/ # 
```
7、退出busybox
```
/ # exit
```

查看应用程序日志

1、获取命名空间中pod状态
```
kubectl get pods -n development        
NAME                                   READY   STATUS    RESTARTS   AGE
pod-info-deployment-7b697c564d-6fg5x   1/1     Running   0          6d21h
pod-info-deployment-7b697c564d-wrgsz   1/1     Running   0          6d21h
pod-info-deployment-7b697c564d-z4rk6   1/1     Running   0          6d21h
```
2、是用logs获取pod节点运行日志
```
kubectl logs pod-info-deployment-7b697c564d-6fg5x -n development                                                      
undefined
Example app listening on port 3000
```



[参考]
- https://kubernetes.io/zh-cn/docs/concepts/overview/