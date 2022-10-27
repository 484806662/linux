# 基于阿里云搭建局域网内部YUM仓库

|                       | 实验环境：  |          |
| --------------------- | ----------- | -------- |
| Yum源仓库服务器IP地址 | 172.16.30.1 | CentOS 7 |
| Yum源测试客户端IP地址 | 172.16.10.1 | CentOS 7 |

## 一、添加阿里云yumhttp源并检查

`mkdir /tmp/yum`

`mv /etc/yum.repos.d/* /tmp/yum/`

`curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo`
`curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo`

`yum makecache;yum repolist`

## 二、安装相关软件

`yum install -y wget make cmake gcc gcc-c++ pcre-devel zlib-devel openssl openssl-devel httpd yum-utils createrepo`

```
标注：yum-utils：reposync同步工具
createrepo：编辑yum库工具
httpd：通过Apache软件提供web服务，也可以使用nginx
```

## 三、同步阿里云yum源软件包到本地服务器指定目录/mirror（自定义目录）

`mkdir -p /mirror`
`chown -R apache:apache /mirror`
`chmod -R 755 /mirror`

### 参数-n指下载最新软件包，-p指定目录，指定本地的源--repoid（如果不指定就同步本地服务器所有的源）,下载过程比较久

`reposync -n --repoid=extras --repoid=updates --repoid=base --repoid=epel -p /mirror`

`[root@yumserver ~]# du -sh /mirror/*`

```
9.0G   /mirror/base

16G   /mirror/epel

321M   /mirror/extras

2.2G   /mirror/updates
```

## 四、创建仓库索引

`createrepo -po /mirror/base/ /mirror/base/`

`createrepo -po /mirror/extras/ /mirror/extras/`

`createrepo -po /mirror/updates/ /mirror/updates/`

`createrepo -po /mirror/epel/ /mirror/epel/`

```
问题：如果创建仓库索引提示如下错误

Worker 0: Error: Could not open local rpm file: /mirror/epel//Packages/p/python2-pycryptodomex-3.9.7-1.el7.x86_64.rpm: RPM Error opening Package

解决办法：提示哪个软件包打开错误就删除哪个软件包

rm -rf  /mirror/epel//Packages/p/python2-pycryptodomex-3.9.7-1.el7.x86_64.rpm
```

## 五、更新数据源

`createrepo --update /mirror/base/`

`createrepo --update /mirror/extras/`

`createrepo --update /mirror/updates/`

`createrepo --update /mirror/epel/`

快捷方式

`createrepo --update /mirror/base/;createrepo --update /mirror/extras/;createrepo --update /mirror/updates/;createrepo --update /mirror/epel/`

## 六、启动并配置Apacheghttp服务

`systemctl start httpd`

`systemctl enable httpd`

`systemctl status httpd`

快捷方式

`systemctl start httpd;systemctl enable httpd;systemctl status httpd`

`[root@yumserver ~]# vim /etc/httpd/conf/httpd.conf`

### 编写如下内容

```
DocumentRoot "/mirror/"
<Directory "/mirror/">
    Options Indexes FollowSymLinks
    AllowOverride  None
    Order allow,deny
    Allow from all
    Require all granted
</Directory>
```

### 修改Apache默认首页index.html，直接复制粘贴执行

`vim /usr/share/httpd/noindex/index.html`

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>CentOS 7 镜像</title>

<script>document.createElement("myHero")</script>
<style>
myHero {
        display: block;
        background-color: #ddd;
        padding: 10px;
        font-size: 20px;
} 
</style> 
</head>
<body>

    <h1>简介</h1>
    <hr>
    <p>CentOS，是基于 Red Hat Linux 提供的可自由使用源代码的企业级 Linux 发行版本，是一个稳定，可预测，可管理和可复制的免费企业级计算平台。</p>
    <hr>
    <br>
    <br>

        <h1>CentOS 7 配置内部YUM源</h1>
    <br>
        <h2>1、备份</h2>
        <myHero>mv /etc/yum.repos.d/* /opt/yum/</myHero>
    <br>
        <h2>2、下载新的 CentOS-Base.repo 到 /etc/yum.repos.d/ </h2>
        <myHero>curl -o /etc/yum.repos.d/CentOS-Base.repo http://10.10.10.20/repo/CentOS-Base.repo</myHero>
    <br>
        <h2>3、运行 yum makecache 生成缓存</h2>
    <br>
        <h2>4、运行 yum repolist   查看已经生成缓存</h2>
    <br>
    <br>

</body>
</html>

***修改其中的10.10.10.20为172.16.30.1***

## 七、编写yumhttp源客户端配置文件



`[root@yumserver ~]# mkdir -p /mirror/repo/`

`vi/mirror/repo/CentOS-Base.repo`

```
[base]
name=CentOS-$releasever - Base - 10.10.10.20
failovermethod=priority
baseurl=http://10.10.10.20/base/
enable=1
gpgcheck=0
#released updates 
[updates]
name=CentOS-$releasever - Updates - 10.10.10.20
failovermethod=priority
baseurl=http://10.10.10.20/updates/
enable=1
gpgcheck=0
#additional packages that may be useful
[extras]name=CentOS-$releasever - Extras - 10.10.10.20
failovermethod=priority
baseurl=http://10.10.10.20/extras/
enable=1
gpgcheck=0
#additional packages that may be useful
[epel]name=CentOS-$releasever - Epel - 10.10.10.20
failovermethod=priority
baseurl=http://10.10.10.20/epel/
enable=1
gpgcheck=0
```

***修改其中的10.10.10.20为172.16.30.1***

## 八、客户端配置Yum源      

`curl -o /etc/yum.repos.d/CentOS-Base.repo http://10.10.10.20/repo/CentOS-Base.repo`
`yum makecache`
客户端浏览网页http://10.10.10.20首页提示如何配置

![image-20221027093320635](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20221027093320635.png)