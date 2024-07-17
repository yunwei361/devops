# 监听服务状态实现服务高可用

keepalived 为系统级的高可用（机器挂了的情况才会切换），若是想实现服务级的高可用（某一服务挂了就切换主备），还需额外配置监听脚本

> 主要配置项为：
>
> vrrp\_script
>
> track\_script



### 案例演示：

此案例使用抢占模式，脚本为监听nginx服务的状态

#### 主节点配置

```keepalived
global_defs {
   router_id LB_01
}

vrrp_script nginx_check {
    script /root/nginx_check_status.sh
    interval 5
    weight 1
    user root
}

vrrp_instance VI_1 {
    state MASTER
    interface ens34
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.31.8
    }
    track_script {
        nginx_check
    }
}
```

#### 备节点配置

```keepalived
global_defs {
   router_id LB_02
}

vrrp_script nginx_check {
    script /root/nginx_check_status.sh
    interval 5
    weight 1
    user root
}

vrrp_instance VI_1 {
    state BACKUP
    interface ens34
    virtual_router_id 51
    priority 50
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.31.8
    }
    track_script {
        nginx_check
    }
}
```

#### 监听脚本：nginx\_check\_status.sh

```sh
#!/bin/bash

check_cmd=$(pidof nginx 2> /dev/null)

if [ -z "$check_cmd" ]; then
    systemctl stop keepalived
fi
```

使用脚本前需要赋执行权限

```bash
chmod +x nginx_check_status.sh
```
