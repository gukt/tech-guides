修改心跳间隔以及相关参数，打开`/etc/ssh/sshd_config`文件

```sh
$ vim /etc/ssh/sshd_config
```

找到以下两行，更改如下配置项

```sh
#ClientAliveInterval:表示发送心跳的间隔(秒)
#ClientAliveCountMax:最大允许多少次心跳没响应
ClientAliveInterval 60
ClientAliveCountMax 300
#这样设置以后，300分钟（5小时）后服务器才会主动断开连接
#保存后退出
:wq
```

重启sshd

```sh
$ service sshd restart
```

