---
title: Bash
date: 2022-01-12
category:
  - Language
tag:
  - bash
sticky: false
---

# Bash



## 1 编程风格：

:tada:Bash是个灵活度很高的脚本语言，好的编码风格阅读起来事半功倍。:tada:

::: tip
1、缩进用四个空格。 不要用制表符

2、代码超过30行都要放在函数中，并且由main函数启动。 不要写流水账

3、变量要注意作用域readonly和local按照需求声明好。 不要随意使用全局变量

4、函数要function check_disk()的方式声明。 不要用check_disk()的方式

5、逻辑判断用if [[ ${json} != "fun" ]]。 不要用test、[]的方式

6、if|while|for 循环分支逻辑在本行使用; then|; do。 不要另起一行

:::
> 调试代码要比写代码困难两倍。因此，你写代码时越多的使用奇技淫巧（自做聪明），你越难以调试它。 --Brian Kernighan


Hello World
```
#!/bin/bash

var="hello world";
echo "$var"

#执行
[root@localhost ~]# sh test.sh 
hello world
```

## 2 基础概念：

### 2.1 变量

```bash
#变量赋值
test_str="hello world"

#变量默认值
${hostname:="127.0.0.1"}
```


### 2.2 read：

```bash
#!/bin/bash

read -p "请输入你的名字:" name
echo ${name}
```

### 2.3 for循环
下面这两种形式

```bash
#!/bin/bash

array=(1 2 3)

for line in ${array};do 
   echo ${line}
done

for((i=1;i<=5;i++)); do
  echo "weclome $i"
done
```

### 2.4 while循环

```bash
#!/bin/bash

i=1
while((i<=5)); do
  echo "welcome $i"
  let i++
done
```

### 2.5 case匹配

```bash
#!/bin/bash

case $1 in
start)
   echo "starting"
   ;;
stop)
   echo "stoping"
   ;;
*)
  echo "没有匹配的"
esac
```

### 2.6 if判断

```bash
#!/bin/bash

read -p "请输入用户名" NAME
printf '%s\n' $NAME

if [[ $NAME = root ]] || [[ $NAME =~ "usr" ]]; then
    echo "欢迎你 ${NAME}"
elif [[ $NAME = magic ]]; then
    echo "欢迎你，${NAME}"          
else
    echo "你是谁，886"
fi
```

::: details 判断参数详情
```txt
-e 判断对象是否存在
-d 判断对象是否存在，并且为目录
-f 判断对象是否存在，并且为常规文件
-L 判断对象是否存在，并且为符号链接
-h 判断对象是否存在，并且为软链接
-s 判断对象是否存在，并且长度不为0
-r 判断对象是否存在，并且可读
-w 判断对象是否存在，并且可写
-x 判断对象是否存在，并且可执行
-O 判断对象是否存在，并且属于当前用户
-G 判断对象是否存在，并且属于当前用户组
-nt 判断file1是否比file2新  [ "/data/file1" -nt "/data/file2" ]
-ot 判断file1是否比file2旧  [ "/data/file1" -ot "/data/file2" ]
```
:::

### 2.7 字符串

```bash
#获取字符串长度
string="abcd"echo ${#string} #输出 4

#提取子字符串 
#以下实例从字符串第 2 个字符开始截取 4 个字符：
string="turtle is a great site"echo ${string:1:4} # 输出 le

#查找子字符串
#查找字符 i 或 o 的位置(哪个字母先出现就计算哪个)：
str="runoob is a great site"
echo `expr index "$str" io` 
4

#字符串替换
$ foo=JPG.JPG
$ echo ${foo/#JPG/jpg}
jpg.JPG

#改变大小写
$ foo=heLLo
$ echo ${foo^^}
HELLO
$ echo ${foo,,}
hello
```



### 2.8 计算

```bash
$ echo $((2 + 2))
4
$ echo $((5 / 2))
2
$ echo $(((5**2) * 3))
75
$ echo $(( (3 > 2) || (4 <= 1) ))
1
```

### 2.9 三元表达式

```bash
#!/bin/bash
var=$1
res=$([ "$var" == 123 ] && echo true || echo false)
echo "$res"

[root@localhost ~]# sh test.sh 123
true
[root@localhost ~]# sh test.sh 1234
false
```

### 2.10 数组

```bash
(1)定义数组
#在 Shell 中，用括号来表示数组，数组元素用"空格"符号分割开。
array_name=(value0 value1 value2 value3)

(2)读取数组
读取数组元素值的一般格式是：
${数组名[下标]}
例如
valuen=${array_name[n]}
#使用 @ 符号可以获取数组中的所有元素：
echo ${array_name[@]}

(3)获取数组的长度
获取数组长度的方法与获取字符串长度的方法相同，例如：
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}

(4)删除数组
$ foo=(a b c d e f)
$ echo ${foo[@]}
a b c d e f

$ unset foo[2]
$ echo ${foo[@]}
a b d e f
```

### 2.11 哈希表
```bash
#!/bin/bash

ARRAY=( "zhangsan:7123"
        "lisi:9212"
        "wangwu:6323"
        "zhaoliu:3212" )

#获取第2个元素
echo -e "key:${ARRAY[2]%%:*}\tvalue:${ARRAY[2]#*:}\n"

#遍历所有元素
for shard in "${ARRAY[@]}"; do
    key=${shard%%:*}
    value=${shard#*:}
    echo -e "key:${key}\tvalue:${value}";
done


[root@localhost ~]# sh hashtable.sh
key:wangwu	value:6323

key:zhangsan	value:7123
key:lisi	     value:9212
key:wangwu	   value:6323
key:zhaoliu	   value:3212
```

### 2.12 函数

```bash
#!/bin/bash

function print_sum() {
    #分别表示第一个参数，第二个参数
    args1=$1
    args2=$2
    echo "input args1, $args1"
    echo "input args2, $args1"
    return $((${args1} + ${args2}))
}
print_sum 12 13
echo "$?"

[root@loacl ~]# sh test.sh 
input args1, 12
input args2, 12
25
--------------------------------------------------------------
$? 表示上一个命令退出的状态,0表示执行正常，不等于0表示执行不正常。
$$ 表示当前进程编号
$0 表示当前脚本名称
$# 表示参数的个数，常用于循环
$*和$@ 都表示参数列表 
$n 表示n位置的输入参数（n代表数字，n>=1）
--------------------------------------------------------------
```

### 2.13 文本处理
#### awk
```bash
awk '{print $5}' file              # 打印文件中以空格分隔的第五列
awk -F ',' '{print $5}' file       # 打印文件中以逗号分隔的第五列
awk '/str/ {print $2}' file        # 打印文件中包含 str 的所有行的第二列
awk -F ',' '{print $NF}' file      # 打印逗号分隔的文件中的每行最后一列 
awk '{s+=$1} END {print s}' file   # 计算所有第一列的合
awk 'NR%3==1' file                 # 从第一行开始，每隔三行打印一行
```
#### sed
```bash
sed 's/find/replace/' file         # 替换文件中首次出现的字符串并输出结果 
sed '10s/find/replace/' file       # 替换文件第 10 行内容
sed '10,20s/find/replace/' file    # 替换文件中 10-20 行内容
sed -r 's/regex/replace/g' file    # 替换文件中所有出现的字符串
sed -i 's/find/replace/g' file     # 替换文件中所有出现的字符并且覆盖文件
sed -i '/find/i\newline' file      # 在文件的匹配文本前插入行
sed -i '/find/a\newline' file      # 在文件的匹配文本后插入行
sed '/line/s/find/replace/' file   # 先搜索行特征再执行替换
sed -e 's/f/r/' -e 's/f/r' file    # 执行多次替换
sed 's#find#replace#' file         # 使用 # 替换 / 来避免 pattern 中有斜杆
sed -i -r 's/^\s+//g' file         # 删除文件每行头部空格
sed '/^$/d' file                   # 删除文件空行并打印
sed -i 's/\s\+$//' file            # 删除文件每行末尾多余空格
sed -n '2p' file                   # 打印文件第二行
sed -n '2,5p' file                 # 打印文件第二到第五行
```

#### grep
正则过滤
```bash
###############################################
# ^_              匹配以下划线`_`为开始
# [a-zA-Z]{3,5}   匹配3-5个大小写字母
# :\*:            匹配`:*:`字符
# [7-9].          匹配1-9开头后面`.`代表一个占位符
# :[0-9]{2}       匹配`:`加上2位0-9的数字
###############################################

[root@dev ~]# cat /etc/passwd|grep -E "^_[a-zA-Z]{3,5}:\*:[7-9].:[0-9]{2}"

_www:*:70:70:World Wide Web Server:/Library/WebServer:/usr/bin/false
_eppc:*:71:71:Apple Events User:/var/empty:/usr/bin/false
_cvs:*:72:72:CVS Server:/var/empty:/usr/bin/false
_svn:*:73:73:SVN Server:/var/empty:/usr/bin/false
_mysql:*:74:74:MySQL Server:/var/empty:/usr/bin/false
_sshd:*:75:75:sshd Privilege separation:/var/empty:/usr/bin/false
_qtss:*:76:76:QuickTime Streaming Server:/var/empty:/usr/bin/false
```

#### sort
```bash
sort file                          # 排序文件
sort -r file                       # 反向排序（降序）
sort -n file                       # 使用数字而不是字符串进行比较
sort -t: -k 3n /etc/passwd         # 按:切分 passwd 文件的第三列进行排序
sort -u file                       # 去重排序
sort -h file                       # 支持 K/M/G 等量级符号，可与 du 结合使用
```


### 2.14 颜色

```txt
#字体
\033[1;37m：白色    \033[1;33m：黄色
\033[0;30m：黑色    \033[1;30m：深灰色
\033[0;31m：红色    \033[1;31m：浅红色
\033[0;32m：绿色    \033[1;32m：浅绿色
\033[0;33m：棕色    \033[0;37m：浅灰色
\033[0;34m：蓝色    \033[1;34m：浅蓝色
\033[0;35m：粉红    \033[1;35m：浅粉色
\033[0;36m：青色    \033[1;36m：浅青色

#背景
\033[0;40m：蓝色    \033[1;44m：黑色
\033[0;41m：红色    \033[1;45m：粉红
\033[0;42m：绿色    \033[1;46m：青色
\033[0;43m：棕色    \033[1;47m：浅灰色
```


## 3 其他：

::: details 错误处理
```bash
err() {
    echo "[$(date +'%Y-%m-%dT%H:%M:%S%z')]: $@" >&2
}

if ! do_something; then
    err "Unable to do_something"
    exit "${E_DID_NOTHING}"
fi
```
:::


::: details 进度条：
```bash
#!/usr/bin/bash

function progress(){
    local out=''
    for ((i=0;$i<=100;i+=1)); do
            printf "progress:[%-100s] %d%%\r" ${out} $i
            sleep 0.01
            local out=#${out}  
    done
    echo ''
}

progress


[root@dev ~]# sh print200.sh 
progress:[##################################################################]100%
```
:::


## 4 案例：

### 案例一：
::: details 根据pid获取进程的执行信息`show_pid_info.sh`
```bash
#! /bin/bash

read -p "请输入要查询的PID: " P

n=`ps -aux| awk '$2~/^'${P}'$/{print $0}'|wc -l`

if [ $n -eq 0 ];then
 echo "该PID不存在！！"
 exit
fi
echo -e "\e[32m--------------------------------\e[0m"
echo "进程PID: ${P}"
echo "进程命令：$(ps -aux| awk '$2~/^'$P'$/{for (i=11;i<=NF;i++) printf("%s ",$i)}')"
echo "进程所属用户: $(ps -aux| awk '$2~/^'$P'$/{print $1}')"
echo "CPU占用率：$(ps -aux| awk '$2~/^'$P'$/{print $3}')%"
echo "内存占用率：$(ps -aux| awk '$2~/^'$P'$/{print $4}')%"
echo "进程开始运行的时间：$(ps -aux| awk '$2~/^'$P'$/{print $9}')"
echo "进程运行的时间：$(ps -aux| awk '$2~/^'$P'$/{print $10}')"
echo "进程状态：$(ps -aux| awk '$2~/^'$P'$/{print $8}')"
echo "进程虚拟内存：$(ps -aux| awk '$2~/^'$P'$/{print $5}')"
echo "进程共享内存：$(ps -aux| awk '$2~/^'$P'$/{print $6}')"
echo -e "\e[32m--------------------------------\e[0m"
```
使用方式：
```bash
[root@localhost ~]# sh show_pid_info.sh
请输入要查询的PID: 1
--------------------------------
进程PID: 1
进程命令：/usr/lib/systemd/systemd --switched-root --system --deserialize 22 
进程所属用户: root
CPU占用率：0.0%
内存占用率：0.2%
进程开始运行的时间：Jul28
进程运行的时间：0:01
进程状态：Ss
进程虚拟内存：128160
进程共享内存：3720
--------------------------------
````
:::

### 案例二
::: details 系统负载查看`show_cpu_info.sh`
```bash
#!/bin/bash

#物理内存使用量
mem_used=$(free -m | grep Mem | awk '{print$3}')

#物理内存总量
mem_total=$(free -m | grep Mem | awk '{print$2}')

#cpu核数
cpu_num=$(lscpu  | grep 'CPU(s)' | awk 'NR==1 {print$2}')

#平均负载
load_average=$(uptime  | awk -F : '{print$5}')

#用户态的CPU使用率
cpu_us=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $1}' | awk '{print $(NF-1)}')

#内核态的CPU使用率
cpu_sys=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $2}' | awk '{print $(NF-1)}')

#等待I/O的CPU使用率
cpu_wa=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $5}' | awk '{print $(NF-1)}')

#处理硬中断的CPU使用率
cpu_hi=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $6}' | awk '{print $(NF-1)}')

#处理软中断的CPU使用率
cpu_si=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $7}'| awk '{print $(NF-1)}')

echo -e "物理内存使用量(M)为：${mem_used}"
echo -e "物理内存总量(M)为：${mem_total}"
echo -e "cpu核数为：${cpu_num}"
echo -e "平均负载为：${load_average}"
echo -e "用户态的CPU使用率为：${cpu_us}"
echo -e "内核态的CPU使用率为：${cpu_sys}"
echo -e "等待I/O的CPU使用率为：${cpu_wa}"
echo -e "处理硬中断的CPU使用率为：${cpu_hi}"
echo -e "处理软中断的CPU使用率为：${cpu_si}"
```
:::

### 案例三
随着postman越来越慢占用资源越来越大，有必要使用bash+curl代替它；微服务后台直接测试接口速度酸爽很多。
::: details postman.h
```bash
#!/usr/bin/bash

##############################
#
#des:API测试脚本
#date:20210901
#
##############################

SERVICE_HOST="http://localhost:443/v1/api/xxservice"


function get_token(){
    local token_api="${SERVICE_HOST}/token"

    local res=$(curl -k -H "Content-Type: application/json" -XPOST ${token_api} -d \
                '{"username":"xxxxxxxxxx",
                  "password":"yyyyyyyyyyyy",
                  "date":"1609430400000"}'
               )

    echo "API: ${token_api}"
    echo "token: ${res:=null}"
}

function help(){
    echo -e "where options include:"
    echo -e "\t-help              \t脚本帮助文档"
    echo -e "\t-version           \t脚本版本信息"
    echo -e "\t-get_token         \t获取token认证";
}

function version(){
    echo -e "ScriptName: rest_api_dev.sh"
    echo -e "\tV1.0"
}

function main(){
    case "$1" in
        -help)                help;;
        -version)             version;;
        -get_token)           get_token;;
        *)                    echo -e "Usages：rest_api_dev.sh [-options] \n"; help;;
    esac
}

main $@
```
:::

[参考]
- https://wangdoc.com/bash/
- https://google.github.io/styleguide/shell.xml
- https://github.com/Bash-it/bash-it
