### Yum安装MySQL5.7

```sh
#配置YUM源，官方地址：http://dev.mysql.com/downloads/repo/yum/
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm

#安装yum源
yum localinstall mysql57-community-release-el7-8.noarch.rpm

#检查yum源是否安装成功
yum repolist enabled | grep "mysql.*-community.*"
mysql-connectors-community/x86_64 MySQL Connectors Community                  51
mysql-tools-community/x86_64      MySQL Tools Community                       63
mysql57-community/x86_64          MySQL 5.7 Community Server                 267
#出现以上信息表示安装YUM源成功

#改变默认安装的mysql版本
vim /etc/yum.repos.d/mysql-community.repo
#打开后，将需要默认安装的版本的enabled设置为1

#安装MySQL
yum install mysql-community-server

#启动mysqld
#service start mysqld
systemctl start mysqld
```



### 查看MySQL5.7的初始密码

```sh
#使用mysql客户端工具连接
mysql -uroot
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

#NOTE: MySQL5.7初始密码不再为空

#查看初始密码
grep 'temporary password' /var/log/mysqld.log 
2018-07-04T08:50:40.380234Z 1 [Note] A temporary password is generated for root@localhost: cSfuOl%!Q3X-

#这个"cSfuOl%!Q3X-"就是初始密码
```



### 密码过期，重设密码 (approach-1)

```sh
#第一次进入mysql客户端，无论执行什么语句都会错误，这是因为密码过期的原因，要重设密码

mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

#重设密码
mysql> SET PASSWORD = PASSWORD('P@s$w0rd#!!32');

#再次执行,OK
mysql> show databases;
```



### 修改MySQL的root密码（version: 5.7）(approach-2)

```sh
#停止mysql服务
service mysqld stop

#安全模式运行mysqld，并禁止网络连接
mysqld_safe --skip-grant-tables --skip-networking &

#使用mysql命令，连接mysqld
mysql -p

#更改密码(NOTE: 新版MySQL的user表中已经没有password字段了)
mysql> UPDATE mysql.user SET authentication_string=password('P@s$w0rd#!!32') WHERE User='root' AND Host = 'localhost';

#修改完毕，重启mysql服务
mysql> flush privileges;
mysql> quit;

service mysqld start
```

 

### 让root账户可以远程登录

**方法1：直接将数据库中已存在的`Host=localhost`的那条记录的`Host`字段改为%`**

```mysql
UPDATE mysql.user SET Host='%' WHERE HOST='localhost' AND User='root';
flush privileges;
```

**NOTE:** 

在mysql.user表中默认有两行数据`root@localhost`, `root@127.0.0.1`，上述命令修改的是root@localhost



**方法2：也可以这样添加一条新账号记录（`'root'@'%'`)，以达到同样的效果**

```mysql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'P@s$w0rd#!!32' WITH GRANT OPTION;
flush privileges;
```

检查看是否添加成功：

```mysql
SELECT User, Password, Host FROM mysql.user WHERE user = 'root'\G;
2 rows in set (0.00 sec)
```



### 修改默认字符集为utf8mb4

```mysql
#修改表的某个字段的字符集
ALTER TABLE cms_musics MODIFY COLUMN fileName VARCHAR(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

#缺省安装后的mysql服务器默认字符集为latin1
SHOW VARIABLES WHERE Variable_name LIKE 'character_set_%' OR Variable_name LIKE 'collation%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
| collation_connection     | utf8_general_ci            |
| collation_database       | latin1_swedish_ci          |
| collation_server         | latin1_swedish_ci          |
+--------------------------+----------------------------+
11 rows in set (0.00 sec)

# 默认的mysql客户端（mysql命令）使用的字符集是utf8
# 但是服务器使用的字符集是latin1, 从character_set_database，character_set_server为latin1可以看出，对应的排序规则是latin1_swedish_ci

# 现在我们来修改服务器的默认字符集和排序规则
# 打开my.cnf(一般在/etc/my.cnf),
vim /etc/my.cnf
# 在[mysqld]组下添加如下几行：
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'

#还可以加上如下行：
[client]
default-character-set=utf8mb4
[mysql]
default-character-set=utf8mb4

# 保存修改后，重启mysqld服务，再次查看字符集以及排序规则
systemctl restart mysqld
mysql -uroot -p
SHOW VARIABLES WHERE Variable_name LIKE 'character_set_%' OR Variable_name LIKE 'collation%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8mb4                    |
| character_set_connection | utf8mb4                    |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8mb4                    |
| character_set_server     | utf8mb4                    |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
| collation_connection     | utf8mb4_unicode_ci         |
| collation_database       | latin1_swedish_ci          |
| collation_server         | utf8mb4_unicode_ci         |
+--------------------------+----------------------------+
11 rows in set (0.00 sec)

#其他都改成了utf8mb4, 数据库还是latin1,因为当前数据库(mycms)是之前创建的，执行如下语句查看之前创建的数据库使用的字符集
mysql> show create database mycms;
+----------+------------------------------------------------------------------+
| Database | Create Database                                                  |
+----------+------------------------------------------------------------------+
| mycms    | CREATE DATABASE `mycms` /*!40100 DEFAULT CHARACTER SET latin1 */ |
+----------+------------------------------------------------------------------+
1 row in set (0.00 sec)

#修改database的字符集
ALTER DATABASE mycms CHARACTER SET utf8mb4;

```



### 修改时区

```sh
#显示当前时区配置信息
show variables like '%time_zone%'

+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone | CST    |
| time_zone        | SYSTEM |
+------------------+--------+

#打开mysql配置文件(mac上通过brew安装的，默认地址在：/usr/local/etc/my.cnf)
vim /usr/local/etc/my.cnf
#在[mysqld]下添加一行
default-time-zone = '+08:00'
#保存退出，重启mysqld
mysql.server restart
#检查是否生效
show variables like '%time_zone%'
```



### 日期相关查询

 ```mysql
-- 查询今天
select *
from nn_orders
where to_days(createTime) = to_days(now());
-- 或者
select *
from nn_orders
where datediff(now(), createTime) = 0;

-- 从昨天开始到现在
select *
from nn_orders
where to_days(now()) - to_days(createTime) <= 1;

-- 昨天
select *
from nn_orders
where to_days(now()) - to_days(createTime) = 1;
-- 或者
select *
from nn_orders
where datediff(now(), createTime) = 1;

-- 过去2天(不包括今天）
select *
from nn_orders
where datediff(now(), createTime) between 1 and 2;

-- 最近3天
select *
from nn_orders
where datediff(now(), createTime) <= 2;

-- 本周
select *
from nn_orders
where yearweek(now()) = yearweek(createTime);

-- 本月
select *
from nn_orders
where year(now()) = year(createTime) and month(now()) = month(createTime);

-- 上月
select *,date_format(now(), '%Y%m'),date_format(createTime, '%Y%m')
from nn_orders
where period_diff(date_format(now(), '%Y%m'), date_format(createTime, '%Y%m')) = 1;

-- 本年
SELECT *
FROM nn_orders
WHERE year(createTime) = year(now());

-- 本季度
select *
from nn_orders
where quarter(createTime) = quarter(now());

-- 上个季度
select
  *,
  quarter(createTime)
from nn_orders
where QUARTER(createTime) = QUARTER(DATE_SUB(now(), interval 1 QUARTER));
 ```



---

### TODO：

- [ ] root@localhost与root@127.0.0.1的 区别
- [ ] 全0地址，localhost， 127.0.0.1
- [ ] shouquan duixiang : 0.0.0.0/0