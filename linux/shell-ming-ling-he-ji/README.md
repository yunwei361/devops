# Shell 命令合集

### 磁盘空间分析

#### 磁盘空间不足，需快速定位目录

```bash
du -x --max-depth=1 / | sort -k 1 -nr
# 统计根目录下磁盘空间使用情况

# -x 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过
# --max-depth 统计第一级目录深度

# -k 按照哪一列进行排序
# -n 对数值进行排序
# -r 反向排序

# 注意：此处du命令没有是用-h参数，用了之后如果出现单位不一致的情况后会导致排序不准
```

#### 系统产生很多碎片文件，导致iNode资源不足

```bash
find / -type f | awk -F "/" -v OFS="/" '{$NF="";dir[$0]++}END{for(i in dir)print dir[i]" "i}' | sort -k 1 -nr | head
```

### CPU和内存分析

#### 查看当前占用CPU最多的几个进程

```shell
ps aux | head -1; ps aux | sort -rn -k3 | head -10
```

#### 查看当前占用内存最多的几个进程

```
ps aux | head -1; ps aux | sort -rn -k4 | head -10
```

#### 查看CPU信息

```sh
# 查看 CPU 型号
cat /proc/cpuinfo | grep "model name" | uniq

# 查看 CPU 个数（主板插槽上的CPU芯片的个数，可能是多路）
cat /proc/cpuinfo | grep "physical id" | sort -u | wc -l

# 查看每颗 CPU 物理核数（一颗物理CPU中包含的内核数量 Core）
cat /proc/cpuinfo | grep "cpu cores" | uniq

# 查看单颗 CPU 逻辑核数（通过超线程技术，能将一个物理核分成多个逻辑核）
cat /proc/cpuinfo | grep "siblings" | uniq
# 查看全部 CPU 逻辑核数
cat /proc/cpuinfo| grep "processor" | wc -l

# 查看是否开启超线程
如果siblings的数量时cpu cores的两倍，则说明支持超线程，并且开启了超线程。
如果数量一样，则说明CPU不支持超线程，或者服务器超线程没打开。（CPU支不支持可根据型号上网查看）
```

#### 查看CPU负载

```sh
top
uptime
# 系统在过去的1分钟、5分钟和15分钟内的平均负载

# 峰值 <=80%，使用率正常
# 峰值 >80%，使用率偏高
# 峰值 >=90%，使用率极高
# 峰值负载 / 核心数 x <= 1，负载正常
# 峰值负载 / 核心数 1 <= x <= 2，负载偏高
# 峰值负载 / 核心数 x >= 2，负载极高�
```

#### 查看OOM情况

```sh
# 查看OOM情况
grep Out /var/log/messages* | tail -n 10
# 多节点
for i in hybrid0{1..3};do ssh $i "hostname -f && sudo grep Out /var/log/messages* | tail -n 10";done

# 查看OOM总次数
grep Out /var/log/messages* | wc -l
# 多节点
for i in hybrid0{1..3};do ssh $i "hostname -f && sudo grep Out /var/log/messages* | wc -l";done�
```

#### 内存信息查看（使用ps\_mem工具）

```sh
# 工具下载
    有外网：sudo yum install -y ps_mem
    无外网：上传ps_mem.py脚本到 /home/will
 
# 查看 will 用户内存占用情况
    # yum，需要使用 root 用户
    top -u will -b -n 1 | grep will |awk '{print $1}'| xargs | tr " " "," | xargs ps_mem -p
    # 脚本
    top -u will -b -n 1 | grep will |awk '{print $1}'| xargs | tr " " "," | xargs python3 ps_mem.py -p
# 查看 will 用户 java 进程内存占用情况
    # yum，需要使用 root 用户
    sudo su - will -c "spadmin jps"|grep -iv jps| awk '{print $1}'| xargs | tr " " "," | xargs ps_mem -sp | awk '{tmp=match($0, /(sa\.log\.file|log\.file)=([a-zA-Z\/\_]+)\.log/, a);if($7 && $NF != "Program"){printf("进程包: %-50s\t日志名称: %-10s\t私有内存: %s%s\t共享内存: %s%s\n",$NF,a[2],$1,$2,$4,$5,$7,$8)}}'
    # 脚本
    sudo su - will -c "spadmin jps"|grep -iv jps| awk '{print $1}'| xargs | tr " " "," | xargs python3 ps_mem.py -sp | awk '{tmp=match($0, /(sa\.log\.file|log\.file)=([a-zA-Z\/\_]+)\.log/, a);if($7 && $NF != "Program"){printf("进程包: %-50s\t日志名称: %-10s\t私有内存: %s%s\t共享内存: %s%s\n",$NF,a[2],$1,$2,$4,$5,$7,$8)}}'
�
```

### 文件操作

#### 批量查找文件作内容替换

```bash
find / -type f -name hello.txt -exec sed -i "s/aaa/ccc/g" {} \;

# -exec 将查找到的内容传递给下一个命令去继续执行相关逻辑
```

#### 批量查找文件作拷贝打包

```bash
(find / -type f -name hello.txt | xargs tar -czf /tmp/hello.tar.gz) && cp -f /tmp/hello.tar.gz /opt/
```

### 网络连接状态分析

#### 了解用户连接请求所建立的网络连接状态

```bash
netstat -n | awk '/^tcp/{++S[$NF]}END{for (a in S)print a,S[a]}'
```

#### 提取主机上的IP信息

```bash
ip addr | grep "global" | awk '{print $2}' | awk -F "/" '{print $1}'
```

### 检测系统中的僵尸进程并将其kill

```shell
ps -e -o stat,ppid,pid,cmd | egrep '^[Zz]' | awk '{print $2}' | xargs kill -9
```

> 这个命令组合是将僵尸进程的父进程kill掉，进而关闭僵尸进程。为什么要这么做呢？其实一般僵尸进程很难直接kill掉，因为僵尸进程已经是死掉的进程，它不能再接收任何信号。所以，需要kill僵尸进程的父进程，这样父进程被kill掉后，僵尸进程就成了孤儿进程，而所有的孤儿进程都会交给系统的1号进程（init或systemd）收养，1号进程会周期性地去调用wait来清除这些僵尸进程。因此可以发现，父进程kill掉之后，僵尸进程也随着消失了，这其实是1号进程作用的结果。

### BASH 常用命令

#### history

```sh
# 查看历史命令记录
history

# 显示最近n条历史命令
 history 10

# 清空内存中命令历史
history -c

# 从文件中恢复历史命令
history -r ～/.

# shell进程保留的历史命令条数
echo $HISTSIZE

# 存放历史命令的文件
echo $HISTFILE


# /etc/bashrc 配置文件
# history
export HISTSIZE=1000
export HISTTIMEFORMAT="%F %T    "
export HISTFILESIZE=100000
arr=($SSH_CLIENT)
ssh_ip=${arr[0]}
export ssh_ip
export HISTFILE=".bash_history_"${ssh_ip}
```
