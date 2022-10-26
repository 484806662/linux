# openstack swift做glance后端存储

 

在controller里操作

vim /etc/glance/glance-api.conf

[glance_store]

default_store = swift          默认为file 改为swift

stores = file, http                默认不用动

swift_store_auth_version = 2        默认版本为2

stores=glance.store.swift.Store,glance.store.filesystem.Store      此项一定要加上，不然无法上传

swift_store_auth_address =  http://controller:5000/v2.0    controller的keystone认证

swift_store_user = service:swift    使用swift用户

swift_store_key = swift        密码

swift_store_container = glance        将要被创建的容器

swift_store_create_container_on_put = True        上传开

swift_store_large_object_size = 5120        最大5G限制，但与glance结合后限制无效

swift_store_large_object_chunk_size = 200        最大200个容器

swift_enable_snet = False



systemctl restart openstack-glance-api



编辑变量，用租户账号密码

vim /root/opensr

export OS_USERNAME=BPM
export OS_PASSWORD=pwd4bpm
export OS_TENANT_NAME=BPM
export OS_AUTH_URL=http://controller:5000/v2.0
export OS_REGION_NAME=RegionOne
export OS_VOLUME_API_VERSION=2

export OS_IMAGE_API_VERSION=1



source /root/opensr

export 验证环境变量



测试。

比如/home下有个做好的img或qcwo2镜像

glance p_w_picpath-create --name "Windows-7-Ultimate-2014.11.19" --file Windows-7-Ultimate-2014.11.19.img --disk-format qcow2 --container-format bare --is-public True --progress

[=============================>] 100%
+------------------+--------------------------------------+
| Property         | Value                                |
+------------------+--------------------------------------+
| checksum         | 777c159ca66db6dc2ae56c683607651d     |
| container_format | bare                                 |
| created_at       | 2015-10-20T06:29:30.899474           |
| deleted          | False                                |
| deleted_at       | None                                 |
| disk_format      | qcow2                                |
| id               | 82676cbe-414f-4c7a-a45b-c4c70aecfde3 |
| is_public        | True                                 |
| min_disk         | 0                                    |
| min_ram          | 0                                    |
| name             | Windows-7-Ultimate-2014.11.19        |
| owner            | d5ed6aee0eae4609a3baa3c4d08e0e64     |
| protected        | False                                |
| size             | 32212254720                          |
| status           | active                               |
| updated_at       | 2015-10-20T06:36:54.885922           |
| virtual_size     | None                                 |
+------------------+--------------------------------------+

glance p_w_picpath-list 查看镜像

接下来启虚机，做快照。测试。


-----------------------------------
©著作权归作者所有：来自51CTO博客作者gushiren的原创作品，请联系作者获取转载授权，否则将追究法律责任
openstack swift做glance后端存储
https://blog.51cto.com/gushiren/1704524