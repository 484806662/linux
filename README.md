

# openstack三节点比赛平台部署

## ip地址规划图

|          |            | 表 1 IP 地址规划 |             |      |
| :------: | :--------: | :--------------: | :---------: | :--: |
| 设备名称 |   主机名   |       接口       |   IP地址    | 说明 |
| 服务器1  | controller |      ens9f0      | 172.16.10.1 | lan  |
|          |            |      ens9f1      |   自定义    |      |
| 服务器2  |  compute1  |      ens9f0      | 172.16.10.2 | lan  |
|          |            |      ens9f1      |   自定义    |      |
| 服务器3  |  compute2  |      ens9f0      | 172.16.10.3 | lan  |
|          |            |      ens9f1      |   自定义    |      |

## 1. 环境配置

### （1）配置主机名

#### 主机密码默认为000000，使用Linux命令修改节点主机名。

```
[root@controller ~]# hostnamectl set-hostname controller
[root@controller ~]# hostname
controller
[root@compute1 ~]# hostnamectl set-hostname compute1
[root@compute1 ~]# hostname
compute1
[root@compute2 ~]# hostnamectl set-hostname compute2
[root@compute2 ~]# hostname
compute2
```

### （2）配置域名解析

#### 修改 hosts 文件将 IP 地址映射为主机名；

```
[root@controller ~]# vi /etc/hosts
…
172.16.10.1 controller
172.16.10.2 compute1
172.16.10.3 compute2

[root@compute1 ~]# vi /etc/hosts
…
172.16.10.1 controller
172.16.10.2 compute1
172.16.10.3 compute2

[root@compute2 ~]# vi /etc/hosts
…
172.16.10.1 controller
172.16.10.2 compute1
172.16.10.3 compute2
```

### （3）配置yum环境

#### 将OpenStack云平台部署的iso文件通过curl命令下载到controller节点的/root目录下。在/opt目录生成centos7-2009和iaas-train目录，将安装镜像文件内容复制到centos7-2009和iaas目录中。

```
[root@controller ~]#  mkdir /opt/{iaas,centos}

[root@controller ~]# mount /root/CentOS-7-x86_64-DVD-2009.iso /mnt/

[root@controller ~]# cp -rfv /mnt/*  /opt/centos

[root@controller ~]# umount /mnt

[root@controller ~]# mount /root/chinaskills_cloud_iaas_v2.0.1.iso /mnt/

[root@controller ~]# cp -rfv /mnt/*  /opt/iaas

[root@controller ~]# umount /mnt/
```

#### 配置controller节点Yum安装源文件yum.repo，指向本地文件目录路径。

`[root@controller ~]#  mkdir /tmp/yum`

`[root@controller ~]# mv /etc/yum.repo.d/* /tmp/yum`

`[root@controller ~]#  vi /etc/yum.repo.d/ftp.repo`

```
[centos] 
name=centos7-2009 
baseurl=file:///opt/centos 
gpgcheck=0 
enabled=1 
[openstack] 
name=openstack
baseurl=file:///opt/iaas/iaas-repo
gpgcheck=0 
enabled=1
```

`[root@controller ~]# yum clean all && yum repolist`

```
x … repo id            repo name                        status centos            centos7-2009                      4,070 openstack         openstack-train                     953 repolist: 5,023
```

#### 在controller节点使用Yum安装vsftpd服务，并将/opt目录下的文件共享出去。

`[root@controller ~]# yum install -y vsftpd`

```
…
Installed:
  vsftpd.x86_64 0:3.0.2-28.el7                                                               
Complete!
[root@controller ~]# echo "anon_root=/opt" >> /etc/vsftpd/vsftpd.conf 
[root@controller ~]# systemctl start vsftpd
[root@controller ~]# systemctl enable vsftpd
```

#### 配置compute节点Yum安装源文件yum.repo，指向controller节点的共享文件目录路径。

`[root@compute1 ~]#  mkdir /tmp/yum`

`[root@compute1 ~]# mv /etc/yum.repo.d/* /tmp/yum`

`[root@compute1 ~]# vi     /etc/yum.repos.d/yum.repo`

```
[centos]	
name=centos7-2009
baseurl=ftp://controller/centos7-2009
gpgcheck=0
enabled=1
[openstack]
name=openstack-train
baseurl=ftp://controller/iaas/iaas-repo
gpgcheck=0
enabled=1
```

`[root@compute1 ~]# yum clean all && yum repolist`

```
…
repo id            repo name                        status
centos            centos7-2009                      4,070
openstack         openstack-train                     953
repolist: 5,023
```

[^compute2 采用同样的命令使Yum安装源文件yum.repo，指向controller节点的共享文件目录路径。]: 
[^如果无法连接controller,可以采用controller的ip]: 

### （4）划分分区

#### 在compute1节点上的磁盘sd4中划分一个5T的分区。

#### 在compute2节点上的磁盘sd4中划分一个5T的分区。

## 2. OpenStack云平台部署

### （1）配置环境变量

#### 在controller节点和compute节点安装OpenStack云平台的安装脚本软件包。

`[root@controller ~]# yum install -y openstack-iaas`

```
…
Installed:
  openstack-train.x86_64 0:v1.0.2-1.el7                                                      
Complete!

[root@compute ~]# yum install -y openstack-iaas
…
Installed:
  openstack-train.x86_64 0:v1.0.2-1.el7                                                      
Complete!
```

#### 在controller节点和compute节点配置环境变量文件/etc/openstack/openrc.sh，按“i”键进入openrc.sh编辑模式：

`[root@controller ~]# vi /etc/openstack/openrc.sh` 

```
HOST_IP=172.16.10.1
HOST_PASS=000000          #controller节点root用户密码
HOST_NAME=controller
HOST_IP_NODE=172.16.10.2
HOST_PASS_NODE=000000     #compute节点root用户密码
HOST_NAME_NODE=compute1
network_segment_IP=172.16.10.0/24
RABBIT_USER=openstack
RABBIT_PASS=000000
DB_PASS=000000
DOMAIN_NAME=demo
ADMIN_PASS=000000
DEMO_PASS=000000
KEYSTONE_DBPASS=000000
GLANCE_DBPASS=000000
GLANCE_PASS=000000
PLACEMENT_DBPASS=000000
PLACEMENT_PASS=000000
NOVA_DBPASS=000000
NOVA_PASS=000000
NEUTRON_DBPASS=000000
NEUTRON_PASS=000000
METADATA_SECRET=000000
INTERFACE_NAME=eth1      #云主机第二张网卡名称
Physical_NAME=provider
minvlan=1
maxvlan=1000
CINDER_DBPASS=000000
CINDER_PASS=000000
BLOCK_DISK=sb4          #compute节点第一个分区名称
SWIFT_PASS=000000
OBJECT_DISK=sb4         #compute节点第二个分区名称
STORAGE_LOCAL_NET_IP=172.16.10.2
```

`[root@compute1 ~]# vi /etc/openstack/openrc.sh` 

[^compute1无需修改]: 



```
HOST_IP=172.16.10.1
HOST_PASS=000000          #controller节点root用户密码
HOST_NAME=controller
HOST_IP_NODE=172.16.10.2
HOST_PASS_NODE=000000     #compute节点root用户密码
HOST_NAME_NODE=compute1
network_segment_IP=172.16.10.0/24
RABBIT_USER=openstack
RABBIT_PASS=000000
DB_PASS=000000
DOMAIN_NAME=demo
ADMIN_PASS=000000
DEMO_PASS=000000
KEYSTONE_DBPASS=000000
GLANCE_DBPASS=000000
GLANCE_PASS=000000
PLACEMENT_DBPASS=000000
PLACEMENT_PASS=000000
NOVA_DBPASS=000000
NOVA_PASS=000000
NEUTRON_DBPASS=000000
NEUTRON_PASS=000000
METADATA_SECRET=000000
INTERFACE_NAME=eth1      #云主机第二张网卡名称
Physical_NAME=provider
minvlan=1
maxvlan=1000
CINDER_DBPASS=000000
CINDER_PASS=000000
BLOCK_DISK=sb4          #compute节点第一个分区名称
SWIFT_PASS=000000
OBJECT_DISK=sb4         #compute节点第二个分区名称
STORAGE_LOCAL_NET_IP=172.16.10.2
```

`[root@compute2 ~]# vi /etc/openstack/openrc.sh` 

[^compute2修改主机名，计算节点ip]: 



```
HOST_IP=172.16.10.1
HOST_PASS=000000          #controller节点root用户密码
HOST_NAME=controller
*HOST_IP_NODE=172.16.10.3
HOST_PASS_NODE=000000     #compute节点root用户密码
*HOST_NAME_NODE=compute2
network_segment_IP=172.16.10.0/24
RABBIT_USER=openstack
RABBIT_PASS=000000
DB_PASS=000000
DOMAIN_NAME=demo
ADMIN_PASS=000000
DEMO_PASS=000000
KEYSTONE_DBPASS=000000
GLANCE_DBPASS=000000
GLANCE_PASS=000000
PLACEMENT_DBPASS=000000
PLACEMENT_PASS=000000
NOVA_DBPASS=000000
NOVA_PASS=000000
NEUTRON_DBPASS=000000
NEUTRON_PASS=000000
METADATA_SECRET=000000
INTERFACE_NAME=eth1      #云主机第二张网卡名称
Physical_NAME=provider
minvlan=1
maxvlan=1000
CINDER_DBPASS=000000
CINDER_PASS=000000
BLOCK_DISK=sb4          #compute节点第一个分区名称
SWIFT_PASS=000000
OBJECT_DISK=sb4         #compute节点第二个分区名称
STORAGE_LOCAL_NET_IP=172.16.10.2
```

### （2）部署OpenStack云平台基础环境

#### 在controller节点和compute节点都执行脚本iaas-pre-host.sh部署OpenStack云平台基础环境。完成后使用reboot命令重启虚拟机以生效配置。

`[root@controller ~]# iaas-pre-host.sh`
`[root@compute1 ~]# iaas-pre-host.sh`

`[root@compute2~]# iaas-pre-host.sh`

### （3）部署Mariadb数据库及Rabbit消息队列服务

#### 在controller节点执行脚本部署Mariadb数据库及Rabbit消息队列服务。

```
[root@controller ~]# iaas-install-mysql.sh
```

### （4）部署Keystone服务

#### 在controller节点执行脚本部署Keystone服务。

```
[root@controller ~]# iaas-install-keystone.sh
```

### （5）部署Glance服务

#### 在controller节点执行脚本部署Glance服务。

```
[root@controller ~]# iaas-install-glance.sh
```

### （6）部署Nova服务

#### 在controller节点执行脚本部署Nova组件的控制服务。

```
[root@controller ~]# iaas-install-placement.sh
[root@controller ~]# iaas-install-nova-controller.sh
```

#### 执行完上面的脚本后，在compute节点执行脚本部署Nova组件的计算服务，这样就将compute节点的CPU、内存及磁盘资源添加到OpenStack云平台的资源池中了。

```
[root@compute1 ~]# iaas-install-nova-compute.sh
[root@compute2 ~]# iaas-install-nova-compute.sh
```

### （7）部署Neutron服务

#### 在controller节点执行脚本部署Neutron组件的控制服务。

```
[root@controller ~]# iaas-install-neutron-controller.sh
```

#### 在compute节点执行脚本部署Neutron组件的计算服务。

```
[root@compute1 ~]# iaas-install-neutron-compute.sh
[root@compute2 ~]# iaas-install-neutron-compute.sh
```

### （8）部署Dashboard服务

#### 在controller节点执行脚本部署Dashboard组件服务。

```
[root@controller ~]# iaas-install-dashboard.sh
```

安装完成后，使用Google浏览器访问OpenStack云平台，访问地址为：http://192.168.100.10/dashboard，domain为demo，用户名为admin，密码为000000。结果如图2和图3所示。

### （9）部署Cinder服务

#### 在compute1节点执行脚本部署Cinder组件服务。

```
[root@compute1 ~]# iaas-install-cinder.sh
```

### （10）部署Swift服务

#### 在compute2节点执行脚本部署Cinder组件服务。

```
[root@compute2 ~]# iaas-install-swift.sh
```

