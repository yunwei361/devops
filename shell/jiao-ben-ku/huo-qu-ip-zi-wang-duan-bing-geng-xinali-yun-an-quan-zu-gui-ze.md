# 获取IP子网段并更新阿里云安全组规则

### update-secgroup.sh

> 此脚本从电信官网获取IP（宽带为电信）并处理成子网网段；
>
> 使用阿里云客户端工具 "aliyun-cli" 更新 ECS 的安全组规则（需提前配置阿里云客户端工具，参考文档：[https://help.aliyun.com/zh/cli/install-cli-on-linux](https://help.aliyun.com/zh/cli/install-cli-on-linux)）

```sh
#!/bin/bash

work_path="/usr/local/update-secgroup"
request=""
SUB_NET=""


get_subnet() {
  request=$(curl -sk https://speedtp3.sc.189.cn:8299/ip/ipv4)

  if [ "$?" -eq 0 ]; then
    PRE_IP=$(echo $request | awk -F '"' '{print $(NF-1)}' | cut -d '.' -f 1,2)
    SUB_NET=${PRE_IP}".0.0/16"
  else
    logs
    exit 1
  fi
}

check_subnet() {
  if ! grep -q "$SUB_NET" "${work_path}/ip_list.txt"; then
    echo $SUB_NET
    update_aliyun_sec_group
    echo $SUB_NET >> ${work_path}/ip_list.txt
    update ${work_path}/ip_list.txt
  fi
  logs
}

update_aliyun_sec_group() {
  date +"%F %T" >> ${work_path}/aliyunSecGroup.log
  aliyun ecs AuthorizeSecurityGroup --region cn-chengdu --RegionId cn-chengdu --SecurityGroupId sg-2vc07xnnr7j1ahxq8x9n --SourceCidrIp $SUB_NET --IpProtocol TCP --PortRange 8000/8000 --Description frp >> ${work_path}/aliyunSecGroup.log 2>&1
  aliyun ecs AuthorizeSecurityGroup --region cn-chengdu --RegionId cn-chengdu --SecurityGroupId sg-2vc07xnnr7j1ahxq8x9n --SourceCidrIp $SUB_NET --IpProtocol TCP --PortRange 8500/8500 --Description frp_web >> ${work_path}/aliyunSecGroup.log 2>&1
  echo >> ${work_path}/aliyunSecGroup.log
}

logs() {
  date +"%F %T" >> ${work_path}/ip.log
  echo $request >> ${work_path}/ip.log
  echo >> ${work_path}/ip.log
}


main() {
  if ! [ -e "${work_path}/ip_list.txt" ]; then
    curl -o ${work_path}/ip_list.txt https://yunwei361.oss-cn-chengdu.aliyuncs.com/ws/iplist.txt
  fi

  get_subnet
  check_subnet
  
}

main
```
