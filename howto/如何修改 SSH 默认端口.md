打开 `/etc/ssh/ssh_config` 文件：

```sh
$ vim /etc/ssh/sshd_config
```

找到 `Port 22` 行，先暂且保留，复制一行，指定你要的端口号：

```sh
Port 22
Port 10022
```

重启 `sshd`，使修改生效：

```sh
$ systemctl restart sshd
```

使用 `ssh` 命令，连接时指定端口：

```sh
$ ssh -p 10022 root@domain.com
```

连接后，再次编辑 `/etc/ssh/ssh_config` 文件，将端口 `Port 22`  的那行注释掉。

**然后，重启 sshd（切记！）**

```sh
$ service sshd restart
# 或者
$ systemctl restart sshd
```



（完）