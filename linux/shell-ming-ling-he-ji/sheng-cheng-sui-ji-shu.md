# 生成随机数

### $RANDOM

```bash
# 在Linux中，$RANDOM 是一个特殊的环境变量，它用于生成一个伪随机整数。
# 它的范围是从 0 到 32767（即 $RANDOM 的最大值为 32767）

# 输出一个0-32767的随机数
echo $RANDOM

# 输出一个范围在1-100的随机数
echo $((RANDOM%100+1))
```



### /dev/urandom

```bash
# /dev/urandom 设备中读取2个字节，并将其解释为带符号的十六进制整数，禁止输出地址。
# 这将生成一个介于0和65535之间的随机整数
od -An -N2 -i /dev/urandom

# -An：禁止输出地址。通常情况下，od 命令会在每一行输出前显示地址（偏移量），使用 -An 选项禁止输出地址。 
# -N2：指定要读取的字节数。这里的 2 表示要读取的字节数为2个。
# -i：指定输出格式为带符号的十六进制整数。这个选项告诉 od 命令将输入解释为整数，并以带符号的十六进制形式进行输出。

```



### shuf

```bash
# -i 范围
# -n 输出行数
# 下方命令输出1个范围在1-1000内的随机数
shuf -i 1-1000 -n 1

# shuf命令还可以处理文件，可自行研究
```



### 拓展校验命令

#### 时间 + 校验

<pre class="language-bash"><code class="lang-bash"><strong># 生成长度为10位的带数字与小写字母的随机字符串
</strong><strong>date +%N | md5sum | cut -c 1-10
</strong><strong>
</strong><strong># 此处使用date的纳秒为输入，然后对结果生产md5字符串，最后截取前10位作为结果
</strong><strong># 此命令可拓展为生成哈希字符串，然后截取指定长度
</strong></code></pre>

#### /dev/urandom + 校验

```bash
od -An -N4 -i /dev/urandom | sha512sum | cut -c 1-100
```
