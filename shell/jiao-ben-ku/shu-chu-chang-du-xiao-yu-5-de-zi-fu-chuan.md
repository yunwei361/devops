# 输出长度小于5的字符串

#### 方式一：

使用子串实现

```sh
#!/bin/bash

for str1 in Hello world, this is CHN
do
    if [ ${#str1} -lt 5 ]; then
        echo $word
    fi
done
```

#### 方式二：

```sh
#!/bin/bash

for str1 in Hello world, this is CHN
do
    if [ `expr length $str1` -lt 5 ]
        then
            echo $str1
    fi
done
```
