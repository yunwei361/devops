# Some Dockerfile

### JAVA 8 / JDK 1.8.0

Download URL: https://www.oracle.com/java/technologies/downloads/

tar 包：jdk-8u361-linux-x64.tar.gz

#### Dockerfile

```
FROM centos:7.9.2009
MAINTAINER Will_D
 
RUN mkdir -p /usr/lib/jvm
 
ADD ./jdk-8u361-linux-x64.tar.gz /usr/lib/jvm
 
RUN yum -y install openssh-clients kde-l10n-Chinese \
        && yum -y reinstall glibc-common \
        && localedef -c -f UTF-8 -i zh_CN zh_CN.UTF-8 \
        && echo 'LANG="zh_CN.UTF-8"' > /etc/locale.conf \
        && source /etc/locale.conf \
        && yum clean all
 
ENV LANG=zh_CN.UTF-8
ENV LC_ALL=zh_CN.UTF-8
ENV TZ=Asia/Shanghai
ENV JAVA_HOME=/usr/lib/jvm/jdk1.8.0_361
ENV JRE_HOME=/usr/lib/jvm/jdk1.8.0_361/jre
ENV PATH=$JAVA_HOME/bin:$PATH
 
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```

#### 构建及推送

```bash
docker build -t oracle_jdk:1.8.0_361 -f ./Dockerfile .

docker login

docker tag oracle_jdk:1.8.0_361 yunwei361/oracle_jdk:1.8.0_361

docker push yunwei361/oracle_jdk:1.8.0_361
```

#### 备注

如果是OpenJDK，可从官网下载 (https://wiki.openjdk.org/display/jdk8u/Main 点击相关版本的 Binaries 跳转到 github 下载)

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

