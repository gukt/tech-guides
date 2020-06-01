打开vim /etc/ssh/ssh_config

```sh
$ vim /etc/ssh/sshd_config
```

找到Port 22行,先暂且保留，复制一行指定你要的端口号

```sh
Port 22
Port 10022
```

重启sshd,使修改生效

```sh
$ systemctl restart sshd
```

使用ssh连接时指定端口

```sh
$ ssh -p 10022 root@domain.com
```

连接后再打开sshd_config将端口22的那行注释掉，**然后重启sshd（切记）**

