# Linux 文件编码转换和特殊字符替换

### 文件编码

工作中经常遇到一些需要在 Linux 上处理的文件，但是系统分隔符和文件编码不对经常乱码，故需要进行文件编码转换处理。下方案例以 CentOS7.9 为系统版本，默认的 utf-8 编码进行演示

#### Windows

如果是在 Windows 系统里，则直接使用 notepad++ 打开文件，然后双击下方状态栏（如图），先将文件转换为 unix 文件。

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

然后点击 encoding，选择相应的编码，即可将文件转换为相应的编码，最后保存，即可完成文件编码的转换

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

#### Linux

```bash
# 使用vim编辑文件
vim example.txt
 
# 查看文件编码
:set fileencoding
 
# 将文件转换为utf-8编码
:set fileencoding=utf-8
 
# 保存文件，即可完成文件编码的转换
:wq
```



### 特殊字符替换

```bash
# 使用vim 编辑文件
vim example.txt
 
# 查看特殊字符对照表，如下图
:set :help digraph-table
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

```bash
# 下方命令为将 , 替换为 ^A  (^A的8进制编码见vim里的对照表为01，命令中需要转码，表示为\o01)
sed -i 's/,/\o01/g' example.txt
```



### 参考链接：

文件编码：https://blog.csdn.net/LiZhen314/article/details/131514730

特殊字符：https://blog.csdn.net/qq\_37067752/article/details/131380282
