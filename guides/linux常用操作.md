## 查看 linux 发行版本号

```sh
$ uname -a
Darwin gukt-mac.local 19.3.0 Darwin Kernel Version 19.3.0: Thu Jan  9 20:58:23 PST 2020; root:xnu-6153.81.5~1/RELEASE_X86_64 x86_64

$ cat /proc/version
Linux version 3.10.0-957.21.3.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) ) #1 SMP Tue Jun 18 16:35:19 UTC 2019

$ cat /etc/issue
\S
Kernel \r on an \m

$ cat /etc/redhat-release
CentOS Linux release 7.7.1908 (Core)
```



## 查看 linux 体系结构

1、使用 `uname -a` 命令

```sh
$ uname -a
Linux gukt-biz1 3.10.0-957.21.3.el7.x86_64 #1 SMP Tue Jun 18 16:35:19 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

> x86_64即是体系结构，如果是 aarch64 表示ARM架构

2、使用 `file` 命令

```sh
$ file /bin/bash
/bin/bash: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=9223530b1aa05d3dbea7e72738b28b1e9d82fbad, stripped
```

3、使用 `arch` 命令

```sh
$ arch
```

4、查看 `/proc/cpuinfo` 文件

```sh
$ cat /proc/cpuinfo
```



## 查看端口占用情况

```sh
# 查看所有TCP端口
$ netstat -ntpl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:6379          0.0.0.0:*               LISTEN      23079/redis-server
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      21231/java
tcp        0      0 0.0.0.0:8082            0.0.0.0:*               LISTEN      26389/java
tcp        0      0 0.0.0.0:8084            0.0.0.0:*               LISTEN      10339/java
tcp        0      0 0.0.0.0:13301           0.0.0.0:*               LISTEN      10339/java
tcp        0      0 0.0.0.0:8085            0.0.0.0:*               LISTEN      12466/java
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      21850/sshd
tcp        0      0 0.0.0.0:3302            0.0.0.0:*               LISTEN      10339/java
tcp6       0      0 :::3306                 :::*                    LISTEN      21533/mysqld     

# 使用lsof命令查看占用端口的进程
$ lsof -i tcp:13301
COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
java    70067 ktgu  219u  IPv6 0x60e648c40af24f2d      0t0  TCP *:13301 (LISTEN)

# 如果没有 lsof 命令，安装一下
$ yum install -y lsof
```



## 查看磁盘占用情况

```sh
# 查看根目录磁盘占用情况
$ df -h /
/dev/vda1        40G   40G     0  100% /
devtmpfs        1.9G     0  1.9G    0% /dev
tmpfs           1.9G     0  1.9G    0% /dev/shm
tmpfs           1.9G  380K  1.9G    1% /run
tmpfs           1.9G     0  1.9G    0% /sys/fs/cgroup
tmpfs           380M     0  380M    0% /run/user/0

# 搜索指定目录中超出指定大小的文件
$ find / -type f -size +500M
/var/log/jenkins/jenkins.log-20180907
/var/log/jenkins/jenkins.log

# 查看这两个文件有多大
$ ll -h /var/log/jenkins/jenkins.log
-rw-r--r-- 1 jenkins jenkins 23G 9月   8 12:00 /var/log/jenkins/jenkins.log
```

