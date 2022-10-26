案例一：使用NFS作为Glance存储后端

```
Linux rpm 命令用于管理套件。
rpm（英文全拼：redhat package manager） 
原本是 Red Hat Linux 发行版专门用来管理 Linux 各项套件的程序，
由于它遵循 GPL 规则且功能强大方便，因而广受欢迎。逐渐受到其他发行版的采用。
RPM 套件管理方式的出现，让 Linux 易于安装，升级，间接提升了 Linux 的适用度。
参数说明：

-a 　查询所有套件。
-b<完成阶段><套件档>+或-t <完成阶段><套件档>+ 　设置包装套件的完成阶段，并指定套件档的文件名称。
-c 　只列出组态配置文件，本参数需配合"-l"参数使用。
-d 　只列出文本文件，本参数需配合"-l"参数使用。
-e<套件档>或--erase<套件档> 　删除指定的套件。
-f<文件>+ 　查询拥有指定文件的套件。
-h或--hash 　套件安装时列出标记。
-i 　显示套件的相关信息。
-i<套件档>或--install<套件档> 　安装指定的套件档。
-l 　显示套件的文件列表。
-p<套件档>+ 　查询指定的RPM套件档。
-q 　使用询问模式，当遇到任何问题时，rpm指令会先询问用户。
-R 　显示套件的关联性信息。
-s 　显示文件状态，本参数需配合"-l"参数使用。
-U<套件档>或--upgrade<套件档> 升级指定的套件档。
-v 　显示指令执行过程。
-vv 　详细显示指令执行过程，便于排错。
-addsign<套件档>+ 　在指定的套件里加上新的签名认证。
--allfiles 　安装所有文件。
--allmatches 　删除符合指定的套件所包含的文件。
--badreloc 　发生错误时，重新配置文件。
--buildroot<根目录> 　设置产生套件时，欲当作根目录的目录。
--changelog 　显示套件的更改记录。
--checksig<套件档>+ 　检验该套件的签名认证。
--clean 　完成套件的包装后，删除包装过程中所建立的目录。
--dbpath<数据库目录> 　设置欲存放RPM数据库的目录。
--dump 　显示每个文件的验证信息。本参数需配合"-l"参数使用。
--excludedocs 　安装套件时，不要安装文件。
--excludepath<排除目录> 　忽略在指定目录里的所有文件。
--force 　强行置换套件或文件。
--ftpproxy<主机名称或IP地址> 　指定FTP代理服务器。
--ftpport<通信端口> 　设置FTP服务器或代理服务器使用的通信端口。
--help 　在线帮助。
--httpproxy<主机名称或IP地址> 　指定HTTP代理服务器。
--httpport<通信端口> 　设置HTTP服务器或代理服务器使用的通信端口。
--ignorearch 　不验证套件档的结构正确性。
--ignoreos 　不验证套件档的结构正确性。
--ignoresize 　安装前不检查磁盘空间是否足够。
--includedocs 　安装套件时，一并安装文件。
--initdb 　确认有正确的数据库可以使用。
--justdb 　更新数据库，当不变动任何文件。
--nobulid 　不执行任何完成阶段。
--nodeps 　不验证套件档的相互关联性。
--nofiles 　不验证文件的属性。
--nogpg 　略过所有GPG的签名认证。
--nomd5 　不使用MD5编码演算确认文件的大小与正确性。
--nopgp 　略过所有PGP的签名认证。
--noorder 　不重新编排套件的安装顺序，以便满足其彼此间的关联性。
--noscripts 　不执行任何安装Script文件。
--notriggers 　不执行该套件包装内的任何Script文件。
--oldpackage 　升级成旧版本的套件。
--percent 　安装套件时显示完成度百分比。
--pipe<执行指令> 　建立管道，把输出结果转为该执行指令的输入数据。
--prefix<目的目录> 　若重新配置文件，就把文件放到指定的目录下。
--provides 　查询该套件所提供的兼容度。
--queryformat<档头格式> 　设置档头的表示方式。
--querytags 　列出可用于档头格式的标签。
--rcfile<配置文件> 　使用指定的配置文件。
--rebulid<套件档> 　安装原始代码套件，重新产生二进制文件的套件。
--rebuliddb 　以现有的数据库为主，重建一份数据库。
--recompile<套件档> 　此参数的效果和指定"--rebulid"参数类似，当不产生套件档。
--relocate<原目录>=<新目录> 　把本来会放到原目录下的文件改放到新目录。
--replacefiles 　强行置换文件。
--replacepkgs 　强行置换套件。
--requires 　查询该套件所需要的兼容度。
--resing<套件档>+ 　删除现有认证，重新产生签名认证。
--rmsource 　完成套件的包装后，删除原始代码。
--rmsource<文件> 　删除原始代码和指定的文件。
--root<根目录> 　设置欲当作根目录的目录。
--scripts 　列出安装套件的Script的变量。
--setperms 　设置文件的权限。
--setugids 　设置文件的拥有者和所属群组。
--short-circuit 　直接略过指定完成阶段的步骤。
--sign 　产生PGP或GPG的签名认证。
--target=<安装平台>+ 　设置产生的套件的安装平台。
--test 　仅作测试，并不真的安装套件。
--timecheck<检查秒数> 　设置检查时间的计时秒数。
--triggeredby<套件档> 　查询该套件的包装者。
--triggers 　展示套件档内的包装Script。
--verify 　此参数的效果和指定"-q"参数相同。
--version 　显示版本信息。
--whatprovides<功能特性> 　查询该套件对指定的功能特性所提供的兼容度。
--whatrequires<功能特性> 　查询该套件对指定的功能特性所需要的兼容度。
```

# 一、NFS服务安装与配置



```
rpm -qa  |grep nfs-utils                              使用询问模式，当遇到任何问题时，rpm指令会先询问用户,查询所有套件。
查询当前基础镜像中安装的nfs服务。
rpm -qa | grep rpcbind
查询当前基础镜像中安装的rpcbind服务

创建一个目录作为nfs的共享目录，命令如下:
mkdir /mnt/test

创建完共享目录后修改配置文件/etc/exports, 在配置文件中加入以下内容：
/mnt/test 10.26.7.0/24(rw,no_root_squash,no_all_squash,sync,anonuid=501,anongid=501)

showmount -e 

显示NFS服务器已公布的共享目录或文件系统列表。
配置文件说明： 
•/mnt/test：为共享目录（若没有这个目录，请新建一个）。
•10.24.200.0/24：可以为一个网段，一个IP，也可以是域名。域名支持通配符，例如， *.qq.com。 
•rw：read-write，只读写。 
•ro：read-only，只读。 
•sync：文件同时写入硬盘和内存。 
•async：文件暂存于内存，而不是直接写入内存。 
•wdelay：延迟写操作。 
•no_root_squash：NFS客户端连接服务端时，如果使用的是root，那么对服务端共享的目录 来说，也拥有root权限。显然开启这项是不安全的。 
•root_squash：NFS客户端连接服务端时，如果使用的是root，那么对服务端共享的目录来 说，拥有匿名用户权限，通常它将使用nobody或nfsnobody身份。 •alL_squash：不论NFS客户端连接服务端时使用什么用户，对服务端共享的目录来说，都拥 有匿名用户权限。 
•anonuid：匿名用户的UID（User Identification，用户身份证明）值，可以在此处自行设 定。 
•anongid：匿名用户的GID（Group Identification，共享资源系統使用者的群体身份）值。 
(3)NFS服务启动 nfs-server端启动NFS服务，命令如下:

二、配置NFC作为glance
rpm -qa |grep nfs-utils
查询当前基础镜像中安装的nfs服务。
mount -t nfs controller:/mnt/test /var/lib/glance/images/

df -hT 查询可以看到已经挂载

source /etc/keystone/admin-openrc.sh  导入环境变量
glance image-create --name cirros --disk-format qcow2 --container-format bare --progress < cirros-0.3.4-x86_64-disk.img  
```



# 二次实验总结：

## 1.先改host名|

## 2.检查是否安装nfs-utils和rpcbind|

## 3.创建一个目录作为nfs的共享目录|

## 4.编辑nfs服务的配置文件/etc/exports, /mnt/test 

`10.26.24.0/24(rw,no_root_squash,no_all_squash,sync,anonuid=501,anongid=501)`

## 5.生效配置 exportfs -r|

## 7.nfs-server端启动nfs服务|

## 8.nfs-server端查看可挂载目录|

## 9.挂载目录 

`mount -t nfs 10.26.24.92:/mnt/test /var/lib/glance/images/`

## 10.查看是否挂载成功，over

`#/mnt/test 10.26.24.0/24(rw,no_root_squash,no_all_squash,sync,anonuid=501,anongid=501)`
`#此命令为本小题的重点。`

# 三次实验总结：

## 修改完配置记得重置服务

```
HTTP 503 Service Unavailable: Insufficient permissions on image storage media: Permission to write image storage media denied.
此错误是没有修改glance权限
解决方式：
[root@controller ~]# cd /var/lib/glance/
[root@controller glance]# chown glance:glance images/
[root@controller glance]# ll
total 0
drwxr-xr-x. 2 glance glance 6 Feb  9 05:56 images
```

