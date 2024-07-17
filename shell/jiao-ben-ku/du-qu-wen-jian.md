# 读取文件

### 读取 data.txt 文件并输出每一行内容

```sh
#!/bin/bash

while read line
do
    echo $line
done < data.txt
```
