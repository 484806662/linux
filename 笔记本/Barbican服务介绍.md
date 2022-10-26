#  Barbican服务介绍

```
Key Manager 服务 (barbican) 提供机密数据的安全存储、配置和管理。这包括密钥材料，例如对称密钥、非对称密钥、证书和原始二进制数据。

Barbican 是 OpenStack 的key管理组件，定位在提供 REST API 来安全存储、提供和管理“秘密”。

Barbican最常用的功能是作为OpenStack环境中的密钥生成器，为各种加解密操作提供支持;同时，Barbican还可以保存OpenStack环境中的用户机密数据。
```

## 1.Barbican服务安装

### （1）编写环境变量文件

#### 使用命令编辑/etc/openstack/openrc.sh配置文件，添加Barbican环境变量参数。

```
##--------------------Barbican Config----------------##
\##Password for Mysql Barbican user. exmaple:000000
BARBICAN_DBPASS=000000

\##Password for Keystore Barbican user. exmaple:000000
BARBICAN_PASS=000000
```

### （2）控制节点安装服务

#### 使用CRT等远程连接工具，连接至172.30.17.14控制节点，执行iaas-install-barbican.sh安装脚本 

```
[root@controller ~]# iaas-install-barbican.sh
```

## 3. 使用Barbican服务

### 使用openstack命令创建一个名为secret01的secret，命令代码如下所示：

```
[root@controller ~]# openstack secret store --name secret01 --payload secretkey
+---------------+------------------------------------------------------------------------+
| Field         | Value                                                                  |
+---------------+------------------------------------------------------------------------+
| Secret href   | http://controller:9311/v1/secrets/08e96ae0-b727-4583-afd9-8ab9de507d4a |
| Name          | secret01                                                               |
| Created       | None                                                                   |
| Status        | None                                                                   |
| Content types | None                                                                   |
| Algorithm     | aes                                                                    |
| Bit length    | 256                                                                    |
| Secret type   | opaque                                                                 |
| Mode          | cbc                                                                    |
| Expiration    | None                                                                   |
+---------------+------------------------------------------------------------------------+
```

### 查询secret列表信息，命令代码如下所示：

```
[root@controller ~]# openstack secret list

+------------------+--------+---------+------+--------------+---------+------+-------+----+----------+
| Secret href      | Name   | Created |Status| Content types|Algorithm|Bit   |Secret |Mode|Expiration|
                                                                       length type   
+------------------+--------+---------+------+--------------+---------+------+-------+----+----------+
|http://controller:|secret01|2022-02- |ACTIVE|{u'default':  | aes     |  256 | opaque|cbc | None     |
 9311/v1/secrets/0           17T09:03          u'text/plain'}
 8e96ae0-b727-4583           :20+00:00
 -afd9-8ab9de507d4a
+------------------+--------+---------+------+--------------+---------+------+-------+----+----------+
```

### 使用命令获取secret01密钥的元数据，命令代码如下所示：

```
[root@controller ~]# openstack secret get http://controller:9311/v1/secrets/08e96ae0-b727-4583-afd9-8ab9de507d4a
+---------------+------------------------------------------------------------------------+
| Field         | Value                                                                  |
+---------------+------------------------------------------------------------------------+
| Secret href   | http://controller:9311/v1/secrets/08e96ae0-b727-4583-afd9-8ab9de507d4a |
| Name          | secret01                                                               |
| Created       | 2022-02-17T09:03:20+00:00                                              |
| Status        | ACTIVE                                                                 |
| Content types | {u'default': u'text/plain'}                                            |
| Algorithm     | aes                                                                    |
| Bit length    | 256                                                                    |
| Secret type   | opaque                                                                 |
| Mode          | cbc                                                                    |
| Expiration    | None                                                                   |
+---------------+------------------------------------------------------------------------+
```

### 通过命令获取secret01密钥的数据，命令代码如下所示：

```
[root@controller ~]# openstack secret get http://controller:9311/v1/secrets/08e96ae0-b727-4583-afd9-8ab9de507d4a --payload
+---------+-----------+
| Field   | Value     |
+---------+-----------+
| Payload | secretkey |
+---------+-----------+
```

### 使用openstack命令生成并存储密钥，命令代码如下所示：

```
[root@controller ~]# openstack secret order create --name secret02 --algorithm aes --bit-length 256 --mode cbc --payload-content-type application/octet-stream key
+----------------+-----------------------------------------------------------------------+
| Field          | Value                                                                 |
+----------------+-----------------------------------------------------------------------+
| Order href     | http://controller:9311/v1/orders/421a8256-79d2-4e53-80ee-c493d4a81317 |
| Type           | Key                                                                   |
| Container href | N/A                                                                   |
| Secret href    | None                                                                  |
| Created        | None                                                                  |
| Status         | None                                                                  |
| Error code     | None                                                                  |
| Error message  | None                                                                  |
+----------------+-----------------------------------------------------------------------+
```

### 通过命令显示生成的密钥列表，命令代码如下所示：

```
[root@controller ~]# openstack secret order list
+-------------------------+----+---------+------------------------+----------+------+------+-------+
| Order href              |Type|Container| Secret href            | Created  |Status|Error |Error  |
                                href                                                 code   message
+-------------------------+----+---------+------------------------+----------+------+------+-------+
|http://controller:9311/v1| Key| N/A     |http://controller:9311/v|2022-02-09|ACTIVE| None | None  |
 /orders/798791cb-297c-4a                 1/secrets/f77fcc45-750e  T08:36:30+
 3d-a8c7-30f6a4dfdd3a                     -4ed2-903d-9af076c26586  00:00
|http://controller:9311/v1| Key| N/A     |http://controller:9311/v|2022-02-17|ACTIVE| None | None |
 /orders/5675a750-bf8f-46                 1/secrets/81b7a19b-2975  T09:13:29+
 68-862c-cb8eedcbb42a                     -4ff0-99a5-ccd5581d1cfa  00:00
+-------------------------+----+---------+------------------------+----------+------+------+-------+
```

### 使用命令显示生成的密钥，命令代码如下所示：

```
[root@controller ~]# openstack secret order get http://controller:9311/v1/orders/5675a750-bf8f-4668-862c-cb8eedcbb42a
+----------------+------------------------------------------------------------------------+
| Field          | Value                                                                  |
+----------------+------------------------------------------------------------------------+
| Order href     | http://controller:9311/v1/orders/5675a750-bf8f-4668-862c-cb8eedcbb42a  |
| Type           | Key                                                                    |
| Container href | N/A                                                                    |
| Secret href    | http://controller:9311/v1/secrets/81b7a19b-2975-4ff0-99a5-ccd5581d1cfa |
| Created        | 2022-02-17T09:30:14+00:00                                              |
| Status         | ACTIVE                                                                 |
| Error code     | None                                                                   |
| Error message  | None                                                                   |
+----------------+------------------------------------------------------------------------+
```

### 显示生成的密钥的元数据，命令代码如下所示：

```
[root@controller ~]# openstack secret get http://controller:9311/v1/secrets/81b7a19b-2975-4ff0-99a5-ccd5581d1cfa 
+---------------+------------------------------------------------------------------------+
| Field         | Value                                                                  |
+---------------+------------------------------------------------------------------------+
| Secret href   | http://controller:9311/v1/secrets/81b7a19b-2975-4ff0-99a5-ccd5581d1cfa |
| Name          | secret01                                                               |
| Created       | 2022-02-17T09:30:14+00:00                                              |
| Status        | ACTIVE                                                                 |
| Content types | {u'default': u'application/octet-stream'}                              |
| Algorithm     | aes                                                                    |
| Bit length    | 256                                                                    |
| Secret type   | symmetric                                                              |
| Mode          | cbc                                                                    |
| Expiration    | None                                                                   |
+---------------+------------------------------------------------------------------------+
```

至此，barbican密钥管理器服务安装完成，上述实验中，只对应熟悉barbican密钥服务所使用的环境操作。