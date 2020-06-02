## 1、安装 mysql yum 仓库

执行以下命令：

```sh
$ wget https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
$ rpm -Uvh mysql80-community-release-el7-1.noarch.rpm
```

> MySQL Yum Repository 下载地址：https://dev.mysql.com/downloads/repo/yum/



## 2、选择版本

安装好后，默认启用的是当前最新版 MySQL（目前是8.0）。

如果需要安装其他版本，请执行如下命令查看有哪些版本可用且当前启用的是哪个版本。

显示MySQL yum仓库中的子仓库列表

```sh
$ yum repolist all | grep mysql
mysql-cluster-7.5-community/x86_64  MySQL Cluster 7.5 Community   disabled
mysql-cluster-7.5-community-source  MySQL Cluster 7.5 Community - disabled
mysql-cluster-7.6-community/x86_64  MySQL Cluster 7.6 Community   disabled
mysql-cluster-7.6-community-source  MySQL Cluster 7.6 Community - disabled
mysql-connectors-community/x86_64   MySQL Connectors Community    enabled:   153
mysql-connectors-community-source   MySQL Connectors Community -  disabled
mysql-tools-community/x86_64        MySQL Tools Community         enabled:   110
mysql-tools-community-source        MySQL Tools Community - Sourc disabled
mysql-tools-preview/x86_64          MySQL Tools Preview           disabled
mysql-tools-preview-source          MySQL Tools Preview - Source  disabled
mysql55-community/x86_64            MySQL 5.5 Community Server    disabled
mysql55-community-source            MySQL 5.5 Community Server -  disabled
mysql56-community/x86_64            MySQL 5.6 Community Server    disabled
mysql56-community-source            MySQL 5.6 Community Server -  disabled
mysql57-community/x86_64            MySQL 5.7 Community Server    disabled
mysql57-community-source            MySQL 5.7 Community Server -  disabled
mysql80-community/x86_64            MySQL 8.0 Community Server    enabled:   177
mysql80-community-source            MySQL 8.0 Community Server -  disabled
```

从列表可以看出默认安装的是 `mysql80-community/x86_64 ` （ `enabled` )。

如果我们要安装 `MySQL5.7` ，使用如下命令：

```sh
#禁用 mysql80
yum-config-manager --disable mysql80-community
#启用 mysql57
yum-config-manager --enable mysql57-community
```

再检查一下哪些子仓库是 enabled 的

```sh
yum repolist enabled | grep mysql
```

除了使用`yum-config-manager`命令修改，还可以直接修改文件`/etc/yum.repos.d/mysql-community.repo`：

```sh
#打开配置文件
vim /etc/yum.repos.d/mysql-community.repo

# 找到以下行，将 enabled 设为 0
[mysql80-community]
name=MySQL 8.0 Community Server
baseurl=http://repo.mysql.com/yum/mysql-8.0-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

# 找到以下行，将 enabled 设为 1
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```



## 3、安装 MySQL

通过 yum install 安装

```sh
$ yum install -y mysql-community-server
```



## 4、启动 mysqld 服务

```sh
$ systemctl start mysqld
```

设置开机自启动

```sh
$ systemctl enable mysqld
```



## 5、查看初始密码

MySQL5.7+ 初始密码不再为空 ( `MySQL5.6` 默认初始密码为空)，使用 `mysql` 客户端工具连接时会提示需要密码

```sh
$ mysql -uroot
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

查看初始密码

```sh
grep 'temporary password' /var/log/mysqld.log 
2018-07-04T08:50:40.380234Z 1 [Note] A temporary password is generated for root@localhost: cSfuOl%!Q3X-
```

这个 `cSfuOl%!Q3X-` 就是初始密码。



（完）

