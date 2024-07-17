# 端口开放检测

### check\_port.sh

```sh
#!/bin/bash

IP=$1
PORT=$2

# 检查端口是否有效
if [[ $PORT =~ ^[1-9][0-9]*$ ]] && [[ $PORT -ge 1 ]] && [[ $PORT -lt 65536 ]]; then
    echo "Right port"
else
    echo "Invalid port"
    exit 1
fi

# 非交互式 telnet 检测端口
echo q | telnet -e q $IP $PORT &> /dev/null

if [ $? -eq 0 ]; then
    echo "Success"
else
    echo "Fail"
fi
```
