# jar 包配置 jmxremote

### JMX 简介

JMX (Java Management Extensions) 是 Java 提供的一套标准 API，用于管理和监控 Java 应用程序的各种性能指标和使用情况。

开启 JMX 后可以将相关指标上传给第三方监控使用（Zabbix），从而达到对 Java 应用程序的监控。



### JMX 启动参数

<table><thead><tr><th width="485">参数</th><th>说明</th></tr></thead><tbody><tr><td>-Dcom.sun.management.jmxremote</td><td>远程开启开关</td></tr><tr><td>-Dcom.sun.management.jmxremote.port</td><td>jmx 远程调用端口</td></tr><tr><td>-Dcom.sun.management.jmxremote.authenticate</td><td>是否开启验证</td></tr><tr><td>-Dcom.sun.management.jmxremote.ssl</td><td>是否用 ssl 连接</td></tr><tr><td>-Djava.rmi.server.hostname</td><td>服务器所在 ip 或者域名</td></tr></tbody></table>



### jar 启用 JMX 启动示例

```bash
java -Dcom.sun.management.jmxremote \
-Dcom.sun.management.jmxremote.port=12345 \
-Dcom.sun.management.jmxremote.authenticate=false \
-Dcom.sun.management.jmxremote.ssl=false \
-Djava.rmi.server.hostname=192.168.1.100 \
-jar xxx.jar
```
