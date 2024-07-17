# 生产案例

### 防脑裂

思路：只要备认为主挂了，就让主真的挂掉

实现方式：备节点有VIP就告警，当收到切换告警后，远程控制主节点，直接关掉keepalived服务



### 降负载

假如公司有两个业务：直播和视频；两个LB，LB01 和 LB02；

当这两个业务都打到LB01上时，LB01可能出现负载过高的情况，LB02则闲置浪费

此时可以设置两组虚拟IP，两个LB互为主备，将直播流量打到LB01，视频流量打到LB02，就可以解决某一台LB负载过高的情况。

#### 节点规划

```
# LB01
192.168.31.21

# LB02
192.168.31.22

# virtual_ipaddress:
192.168.31.8
192.168.31.9
```

#### LB01 节点配置（192.168.31.21:/etc/keepalived/keepalived.conf）

```keepalived
global_defs {
   router_id LB_01
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
}

vrrp_instance VI_2 {
    state BACKUP
    nopreempt
    interface ens34
    virtual_router_id 52
    priority 60
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.31.9
    }
}
```

#### LB02 节点配置（192.168.31.22:/etc/keepalived/keepalived.conf）

```keepalived
global_defs {
   router_id LB_02
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
}

vrrp_instance VI_2 {
    state MASTER
    nopreempt
    interface ens34
    virtual_router_id 52
    priority 110
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.31.9
    }
}
```

