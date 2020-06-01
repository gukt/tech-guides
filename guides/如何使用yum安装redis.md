```sh
$ yum -y install redis

#启动redis-server
#注意：默认情况redis-server进程是一个前台进程，即窗口显示控制台输出，一旦启动窗口关闭，则进程也随即退出。
#如果要想以守护进程启动redis-server,可以修改配置文件redis.conf，然后启动redis-server时指定配置文件

#查看redis的默认配置文件redis.conf所在路径
whereis redis
redis: /etc/redis.conf

#设置redis-server以后台方式运行
vim /etc/redis.conf
#修改'daemonize no'为'daemonize yes',保存退出

#启动redis-server（指定配置文件）
redis-server /etc/redis.conf

#查找进程是否在
ps -ef | grep redis-server

#连接redis-server
redis-cli
127.0.0.1:6379>
```

