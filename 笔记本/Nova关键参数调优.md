### Nova关键参数调优：

```
vcpu_pin_set 建议值是预留前几个物理 CPU，把后面的所有 CPU 分配给虚拟机使用

cpu_allocation_ratio = 4.0 物理 CPU 超售比例，默认是 16 倍，超线程也算作一个物理 CPU，需要根据具体负载和物理 CPU 能力进行综合判断后确定具体的配置

ram_allocation_ratio = 1.0 内存分配超售比例，默认是 1.5 倍，生产环境不建议开启超售。

reserved_host_memory_mb = 4096 内存预留量，这部分内存不能被虚拟机使用

reserved_host_disk_mb = 10240 磁盘预留空间，这部分空间不能被虚拟机使用

service_down_time = 120 服务下线时间阈值，默认60，如果一个节点上的 nova 服务超过这个时间没有上报心跳到数据库，api 服务会认为该服务已经下线，如果配置过短或过长，都会导致误判。

rpc_response_timeout = 300 RPC 调用超时时间，由于 Python 的单进程不能真正的并发，所以 RPC 请求可能不能及时响应，尤其是目标节点在执行耗时较长的定时任务时，所以需要综合考虑超时时间和等待容忍时间。

multi_host = True 是否开启 nova-network 的多节点模式，如果需要多节点部署，则该项需要设置为 True。
```

### keystone关键参数调优：

主要是用来来存储 token，一般是 SQL 数据库，也可以是 memcache。sql 可以持久化存储，而 memcache 则速度更快，尤其是当用户要更新密码的时候，需要删除所有过期的 token，这种情况下 SQL 的速度与 memcache 相差很大很大。

### glance关键参数调优：注意包括glance-api 和 glance-registry

```
workers = 2 glance-api 处理请求的子进程数量，如果配置成 0，则只有一个主进程，相应的配置成 2，则有一个主进程加 2 个子进程来并发处理请求。建议根据进程所在的物理节点计算能力和云平台请求量来综合确定。

api_limit_max = 1000 与 nova 中的配置 osapi_max_limit 意义相同

limit_param_default = 1000一个响应中最大返回项数，可以在请求参数中指定，默认是 25，如果设置过短，可能导致响应数据被截断。
```

## openstack底层依赖软件版本、配置以及调优

1.虚拟化技术选型

我们选用的是 Linux 内核兼容最好的 KVM 虚拟化技术。相对于 Xen 虚拟化技术，KVM 虚拟化技术与 Linux 内核联系更为紧密，更容易维护。选择 KVM 虚拟化技术后，虚拟化管理驱动采用了 OpenStack 社区为 KVM 配置的计算驱动 libvirt，这也是一套使用非常广泛，社区活跃度很高的一套开源虚拟化管理软件，支持 KVM 在内的各种虚拟化管理。

2.CPU配置优化CPU0 由于处理中断请求，本身负荷就较重，不宜再用于云主机。因此，综合上面的因素考虑以及多轮的测试验证，我们最终决定将 0-3 号 CPU 预留出来，然后让云主机在剩余的 CPU 资源中由宿主机内核去调度。

3.内存配置优化

echo 0 > /sys/kernel/mm/ksm/pages_shared

.echo 0 > /sys/kernel/mm/ksm/pages_sharing

echo always > /sys/kernel/mm/transparent_hugepage/enabled

echo never > /sys/kernel/mm/transparent_hugepage/defrag

echo 0 > /sys/kernel/mm/transparent_hugepage/khugepaged/defrag

4.IO配置优化#磁盘IO调优KVM 的 disk cache 方式：借鉴 IBM 的分析，网易私有云采用 none 这种 cache 方式

5.网络IO调优我们主要是开启了 vhost_net 模式，来减少网络延时和增加吞吐量
————————————————
版权声明：本文为CSDN博主「郑广技术博客」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/mtldswz312/article/details/77493680