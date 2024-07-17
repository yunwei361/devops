# iptables 禁 ip

### iptables\_ban\_ip.sh

从 nginx 的 access.log 日志中筛选出访问次数大于 200 次的 IP ，将其禁掉

```sh
#!/bin/bash

logfile=/tmp/access.log
result_file=/tmp/data.txt

# generate result_file
awk '{print $1}' $logfile | sort | uniq -c | sort -rn > $result_file

while read count ip
do
    if [ $count -ge 200 ] && [ $(iptables -nvL | grep -wc $ip) -eq 0 ]; then
        iptables -t filter -I INPUT -s $ip -j DROP
    fi
done < $result_file
```
