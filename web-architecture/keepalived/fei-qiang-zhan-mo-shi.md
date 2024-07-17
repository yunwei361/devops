# 非抢占模式

非抢占模式中，节点1挂了之后节点2会接替1，当1恢复后还是2接替

### 节点规划

```
# 节点1：
192.168.31.21

# 节点2：
192.168.31.22

# virtual_ipaddress：
192.168.31.8
```



### 节点1配置（192.168.31.21:/etc/keepalived/keepalived.conf）

> 配置中以下两项发挥作用：
>
> state BACKUP
>
> nopreempt

```keepalived
global_defs {
   router_id LB_01
}

vrrp_instance VI_1 {
    state BACKUP
    nopreempt
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
}
```



### 节点2配置（192.168.31.22:/etc/keepalived/keepalived.conf）

```keepalived
global_defs {
   router_id LB_02
}

vrrp_instance VI_1 {
    state BACKUP
    nopreempt
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
}
```

