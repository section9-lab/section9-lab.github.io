---
title: LinuxTools
date: 2022-01-12
category:
  - Linux
tag:
  - tools
sticky: false
---

# LinuxTools



## 压缩
```bash
tar.gz格式
解压：tar zxvf FileName.tar.gz
压缩：tar zcvf FileName.tar.gz DirName

tar.tgz格式
解压： tar zxvf FileName.tar.tgz
压缩： tar zcvf FileName.tar.tgz FileName

zip格式
解压：unzip FileName.zip
压缩：zip FileName.zip DirName
```
## curl
```bash
curl -H "Content-Type: application/json" -XPOST localhost:8080/user \
-d  '{"username":"zhangsan_01",
      "age":18,
      "timestamp":"168318059133"}'
```

## sdkman
```sh
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk version

sdk list java
sdk install java 8.0.382-zulu

sdk default java 8.0.382-zulu
sdk current java 
sdk use java 8.0.382-zulu

java -version
```

## tcpdump
```bash
#(1) 捕获指定源地址和目的地址及端口
tcpdump -Xns0 -i eth1 src 192.27.198.179 and dst 100.130.73.95 and dst port 30007 -c10

#(2) 捕获不区分源地址和目的地址
tcpdump -Xns0 -i eth1 host 192.27.198.179 and 100.130.73.95 and  port 30007 -c10

#(3) 捕获主机192.27.198.179和主机192.27.198.169或100.130.73.95的数据包
tcpdump host 192.27.198.179 and \(172.27.198.169 or 10.130.73.95\) and port 30007 -c10

#(4) 捕获主机192.27.198.179除了和主机100.130.73.95之外所有主机通信的IP数据包，!后面要有一个空格
tcpdump ip host 192.27.198.179 and ! 100.130.73.95

#(5) 将捕获的数据包保存在文件中，进行后续分析
tcpdump -Xns0 host 192.27.198.179 -w 179.cap
tcpdump -r 179.cap

#(6) 只显示具体的协议，不显示包体内容
tcpdump -S -nn -vvv -i lo port 6888
-S 打印TCP 数据包的顺序号时, 使用绝对的顺序号, 而不是相对的顺序号
-nn 表示不进行端口到名称的转换
-vvv 表示产生尽可能详细的协议输出

#（7）http请求和响应
tcpdump -XvvennSs 0 -i eth0 tcp[20:2]=0x4745 or tcp[20:2]=0x4854
0x4745 为"GET"前两个字母"GE"
0x4854 为"HTTP"前两个字母"HT"
```
::: details tcpdump详细参数
```txt
————————————————————————————————
-a #将网络地址和广播地址转变成名字
-A #以ASCII格式打印出所有分组，并将链路层的头最小化
-b #数据链路层上选择协议，包括ip/arp/rarp/ipx都在这一层
-c #指定收取数据包的次数，即在收到指定数量的数据包后退出tcpdump
-d #将匹配信息包的代码以人们能够理解的汇编格式输出
-dd  #将匹配信息包的代码以c语言程序段的格式输出
-ddd #将匹配信息包的代码以十进制的形式输出
-D #打印系统中所有可以监控的网络接口
-e #在输出行打印出数据链路层的头部信息
-f #将外部的Internet地址以数字的形式打印出来，即不显示主机名
-F #从指定的文件中读取表达式，忽略其他的表达式
-i #指定监听网络接口
-l #使标准输出变为缓冲形式，可以数据导出到文件
-L #列出网络接口已知的数据链路
-n #不把网络地址转换为名字
-N 不输出主机名中的域名部分，例如www.baidu.com只输出www
-nn #不进行端口名称的转换
-P #不将网络接口设置为混杂模式
-q #快速输出，即只输出较少的协议信息
-r #从指定的文件中读取数据，一般是-w保存的文件
-w #将捕获到的信息保存到文件中，且不分析和打印在屏幕
-s #从每个组中读取在开始的snaplen个字节，而不是默认的68个字节
-S #将tcp的序列号以绝对值形式输出，而不是相对值
-T #将监听到的包直接解析为指定的类型的报文，常见的类型有rpc（远程过程调用）和snmp（简单网络管理协议）
-v #输出稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息
-vv#输出相信的保报文信息
————————————————————————————————
```
:::

## SCP
```bash
本地复制到远程
scp /home/space/music/1.mp3 server01.com:/home/root/others/music/001.mp3 
scp -r /home/space/music/ server01.com:/home/root/others/ 


从远程复制到本地
scp root@server01.com:/home/root/others/music /home/space/music/1.mp3 
scp -r server01.com:/home/root/others/ /home/space/music/
```
## SSH隧道转发

> 环境：开发人员电脑IP:192.168.10.100, 研发团队的开发环境服务器IP:192.168.20.200, 提测环境的服务器IP:192.168.30.123
>
> 为了防止程序员搞乱测试服务器环境，禁止程序员电脑访问测试服务器; 但是程序员和开发服务器,以及开发服务器与测试服务器网络是联通的。


现在就是要通过研发服务器做跳板，让研发人员连接到测试服务器。
### 方案一 本地转发
```bash
#使用个人PC本地的2222端口，用20.122为跳板,访问30.123:22
[root@dev-pc-100 ~]# ssh -L 2222:192.168.30.123:22 -fN 192.168.20.200

[root@dev-pc-100 ~]# ssh 127.0.0.1 -p 2222
[root@test-server-123 ~]#ifconfig
```

### 方案二 远程转发
```bash
#从开发服务器上，操作个人PC的2222端口；
#然后将PC的2222端口的流量通过开发服务器，转发到测试服务器的22端口
[root@dev-server-200 ~]# ssh -R 2222:192.168.30.123:22 -fN 192.168.10.100

[root@dev-pc-100 ~]# ssh 127.0.0.1 -p 2222
[root@test-server-123 ~]#ifconfig
```

## firewall

### 1、服务启停
```bash
sudo systemctl start firewalld
sudo systemctl enable firewalld

sudo systemctl stop firewalld
sudo systemctl disable firewalld
```
### 2、运行状态
```bash
sudo firewall-cmd --state

sudo systemctl status firewalld
```
### 3、重载配置
```bash
sudo firewall-cmd --reload
```
### 4、获取“区域”信息
（针对给定位置或场景 例如家庭、公共、受信任等）
```bash
#获取默认区域
firewall-cmd --get-default-zone

#获取区域网卡接口
firewall-cmd --get-active-zones

#获取区域的所有规则
firewall-cmd --zone=public --list-all
firewall-cmd --list-all-zones
```
### 5、规则
```bash
#允许或禁止12345TCP端口流量
firewall-cmd --zone=public --add-port=12345/tcp --permanent
firewall-cmd --zone=public --remove-port=12345/tcp --permanent

#同一台服务器上将 80 端口的流量转发到 12345 端口
firewall-cmd --zone="public" --add-forward-port=port=80:proto=tcp:toport=12345

#允许来自主机 192.168.0.14 的所有 IPv4 流量
firewall-cmd --zone=public --add-rich-rule 'rule family="ipv4" source address=192.168.0.14 accept'

#拒绝来自主机 192.168.1.10 到 22 端口的 IPv4 的 TCP 流量
firewall-cmd --zone=public --add-rich-rule 'rule family="ipv4" source address="192.168.1.10" port port=22 protocol=tcp reject'

#允许来自主机 10.1.0.3 到 80 端口的 IPv4 的 TCP 流量，并将流量转发到 6532 端口上。 
firewall-cmd --zone=public --add-rich-rule 'rule family=ipv4 source address=10.1.0.3 forward-port port=80 protocol=tcp to-port=6532'

#删除规则
firewall-cmd --zone=public --remove-rich-rule 'rule family=ipv4 source address=10.1.0.3 forward-port port=80 protocol=tcp to-port=6532'
```
## tmux
### 安装
```sh
yum install tmux -y
```
### 基本操作
#### 创建会话
```sh
tmux new -s 00
tmux new-window -n 01
```
#### 后台退出
```sh
tmux detach
```
#### 查看会话
```sh
tmux ls
```
#### 使用会话
```sh
tmux attach -t 00
```

#### 杀死会话
```sh
tmux kill-session -t 00
```
#### 会话内切换会话
```sh
tmux switch -t 01
tmux switch -t 02
```
#### 重命名会话
```sh
tmux rename-session -t 01 001
```

### 窗格操作
#### 进入会话后分割上下窗格
```sh
tmux split-window
```
#### 进入会话后分割左右窗格
```sh
tmux split-window -h
```
#### 切换窗格上下左右
```sh
tmux select-pane -U
tmux select-pane -D
tmux select-pane -L
tmux select-pane -R
```
#### 移动窗格
```sh
tmux swap-pane -U
tmux swap-pane -D
```
配置
```sh
[root@dev ~] cat ~/.tmux.conf
setw -g mouse-resize-pane on
setw -g mouse-select-pane on
setw -g mouse-select-window on
setw -g mode-mouse on
[root@dev ~] tmux source-file ~/.tmux.conf
```
快捷键
```sh
alias tns='tmux new -s'
alias tnw='tmux new-window -n'
alias td='tmux detach'
alias tls='tmux ls'
alias tat='tmux attach -t'
alias tkill='tmux kill-session -t'
alias ts='tmux switch -t'
alias tsw='tmux split-window'
alias tsu='tmux select-pane -U'
alias tsd='tmux select-pane -D'
alias tsl='tmux select-pane -L'
alias tsr='tmux select-pane -R'
```
[参考]
- https://solitum.net/posts/an-illustrated-guide-to-ssh-tunnels/
- https://github.com/wangdoc/ssh-tutorial/
- https://linux.cn/article-8098-1.html
