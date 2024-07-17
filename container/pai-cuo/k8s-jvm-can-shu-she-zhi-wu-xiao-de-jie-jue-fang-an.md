# K8s JVM 参数设置无效的解决方案

### 本地启动

本地环境启动 jar 包会附带启动参数，类似于：

```bash
java -Djava.security.egd=file:/dev/./urandom -jar -XX:InitialRAMPercentage=90.0 -XX:MinRAMPercentage=90.0 -XX:MaxRAMPercentage=90.0 -XX:-UseAdaptiveSizePolicy -XX:+PrintGCDetails -XX:+UseParallelGC -XX:NewRatio=1 -XX:SurvivorRatio=2 /yx-log.jar
```



### 云原生环境启动

#### K8s 设置

到了云原生 K8s 上，-jar 后面的参数就会失效，要让参数生效需要使用环境变量的形式传入。yx 项目使用的是阿里云的 ACK，直接编辑对应服务的 deployment，添加环境变量即可

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

#### Dockerfile 设置

鉴于 yx 项目用的是 Docker，为了减小操作量，这个参数可以直接写到 Dockerfile 里，使用 ENV 传入环境变量，下方为 Dockerfile 示例

```
FROM yxdc-registry.cn-shenzhen.cr.aliyuncs.com/yxdc/cdp-uat:openjdk_8-jre
VOLUME /tmp
ENV TZ=PRC
ENV JAVA_TOOL_OPTIONS -XX:InitialRAMPercentage=90.0 -XX:MinRAMPercentage=90.0 -XX:MaxRAMPercentage=90.0 -XX:-UseAdaptiveSizePolicy -XX:+PrintGCDetails -XX:+UseParallelGC -XX:NewRatio=1 -XX:SurvivorRatio=2
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
ADD yx-log-0.0.1-SNAPSHOT.jar /yx-log.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar", "/yx-log.jar"]
```



### 遇到的坑

变量名称用 JVM\_OPTIONS、JAVA\_OPTIONS 都是无效的，需要使用 JAVA\_TOOL\_OPTIONS 才行

可以进入容器验证，使用如下命令进行验证是否生效

```bash
java -XX:+PrintFlagsFinal -version
java -XshowSettings:all
 
# 部分回显
VM settings:
  Max. Heap Size (Estimated): 807.00M （这个值默认为总内存的25%，可以根据现有总内存乘以设置的百分比算出结果，看是否生效）
  Ergonomics Machine Class: server
  Using VM: OpenJDK 64-Bit Server VM
```
