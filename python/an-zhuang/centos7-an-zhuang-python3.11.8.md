# CentOS7 安装 Python3.11.8

### 下载源代码

```sh
# 下载并解压源代码
# wget -c https://www.python.org/ftp/python/3.9.10/Python-3.9.10.tgz

wget -c https://mirrors.huaweicloud.com/python/3.11.8/Python-3.11.8.tgz
tar xf Python-3.11.8.tgz

mv Python-3.11.8 Python-3.11.8-src
cd Python-3.11.8-src

```



### 安装编译器与依赖

```sh
yum install gcc make -y

yum install bzip2-devel ncurses-devel gdbm-devel sqlite-devel tk-devel readline-devel zlib-devel openssl-devel libffi-devel libuuid-devel xz-devel -y
```



### 配置编译参数与编译

```sh
./configure --enable-optimizations -prefix=/usr/local/Python-3.11.8

# make altinstall
# 使用4个核心编译（查看核心数 grep -c '^processor' /proc/cpuinfo）
make -j 4 && make altinstall
```



### 创建软连接

```sh
mv /usr/bin/python3 /usr/bin/python3_old
mv /usr/bin/pydoc3 /usr/bin/pydoc3_old

ln -s /usr/local/Python-3.11.8/bin/python3.11 /usr/bin/python3
ln -s /usr/local/Python-3.11.8/bin/pydoc3 /usr/bin/pydoc3
ln -s /usr/local/Python-3.11.8/bin/pip3.11 /usr/bin/pip3

```

