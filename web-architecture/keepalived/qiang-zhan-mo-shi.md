# 抢占模式

抢占模式为一主一备的模式，主节点挂了之后备会接替主，当主恢复后主会立刻接替备

### 节点规划

```
# 主 MASTER 节点：
192.168.31.21

# 备 BACKUP 节点：
192.168.31.22

# virtual_ipaddress:
192.168.31.8
```



### 主节点配置（192.168.31.21:/etc/keepalived/keepalived.conf）

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
```



### 备节点配置（192.168.31.22:/etc/keepalived/keepalived.conf）

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
```



### 验证

<pre class="language-bash"><code class="lang-bash"><strong># 两节点都启动服务
</strong><strong>systemctl start keepalived
</strong>systemctl enable keepalived

# MASTER
[root@rocky1 ~]# ip a | grep 192.168.31.8
    inet 192.168.31.8/32 scope global ens34
    
# BACKUP
[root@rocky2 ~]# ip a | grep 192.168.31.8
[root@rocky2 ~]#
</code></pre>

