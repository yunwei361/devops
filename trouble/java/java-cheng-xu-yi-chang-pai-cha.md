# Java 程序异常排查

### 查看日志信息

```bash
cat catalina.out
```



### jmap 导出 jvm 内存镜像

```bash
# jmap -dump:format=b,file=/root/jvm.hprof javapid

jmap -dump:format=b,file=/root/jvm.dump 68565
```
