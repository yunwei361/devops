# CentOS7 安装 Python3.9

### 下载源代码

```sh
# 创建软件目录
mkdir /usr/local/python3.9
cd /usr/local/python3.9

# 下载并解压源代码
# wget -c https://www.python.org/ftp/python/3.9.10/Python-3.9.10.tgz
wget -c https://mirrors.huaweicloud.com/python/3.9.10/Python-3.9.10.tgz
tar xvf Python-3.9.10.tgz
cd Python-3.9.10/

# 国内地址：
https://mirrors.huaweicloud.com/python/
https://mirrors.huaweicloud.com/python/3.9.10/Python-3.9.10.tgz
https://registry.npmmirror.com/binary.html?path=python/
https://registry.npmmirror.com/-/binary/python/3.9.10/Python-3.9.10.tgz
```



### 安装编译器与依赖

```sh
yum install gcc make -y

yum install bzip2-devel ncurses-devel gdbm-devel sqlite-devel tk-devel readline-devel zlib-devel openssl-devel libffi-devel libuuid-devel xz-devel -y
```



### 配置编译参数与编译

```sh
./configure --enable-optimizations -prefix=/usr/local/python3.9

make altinstall
```



### 创建软连接

```sh
cd /usr/bin/
mv python3 python3_old
mv pip3 pip3_old
mv python3-config python3-config_old

ln -s /usr/local/python3.9/bin/python3.9 /usr/bin/python3
ln -s /usr/local/python3.9/bin/pip3.9 /usr/bin/pip3
ln -s /usr/local/Python-3.9.18/bin/python3.9-config /usr/bin/python3-config
```

