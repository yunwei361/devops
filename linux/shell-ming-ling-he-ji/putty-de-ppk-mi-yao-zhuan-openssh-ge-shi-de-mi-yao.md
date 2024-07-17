# PuTTY 的 ppk 密钥转 OpenSSH 格式的密钥

客户给的服务器登陆方式为一个 ppk 文件，经查阅资料，ppk 文件为 putty 软件的密钥，无法直接用于 `ssh` 命令登录，需要转化为 OpenSSH 格式的密钥

### 名词解释

* ppk 文件是 Putty 的私钥，PuTTY Private Key 的缩写
* ppk 文件中同时包含了公钥和私钥，可用记事本打开查看
* id\_rsa 和 id\_rsa.pub 都是 OpenSSH 格式的密钥
* id\_rsa 是 OpenSSH 格式的 SSH 私钥
* id\_rsa.pub 是 OpenSSH 格式的 SSH 公钥

用记事本打开的 ppk 文件内容如下

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>



### 转换步骤

#### 安装 putty 软件（后续会使用软件里的 puttygen 命令进行密钥转换）

```bash
yum install putty -y
```

#### 生成公钥

```bash
puttygen ./ssh.ppk -O public-openssh -o ./id_rsa.pub
```

#### 生成私钥

```bash
puttygen ./ssh.ppk -O private-openssh -o ./id_rsa
```

#### 查看私钥内容

```bash
cat ./id_rsa
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

至此，OpenSSH 格式的密钥就已经转换完毕，可以用来做免密认证。
