免密登陆的原理是：先要在本机生成秘钥对，私钥放在本机，将公钥添加到远程服务器的 `~/.ssh/authorized_keys` 文件中。



## 1、生成密钥对

使用 `ssh-keygen` 命令，生成秘钥对。

```sh
$ cd ~/.ssh
$ ssh-keygen -t rsa -f wow_s1_rsa
```

输入密码，成功后出现以下提示。

```sh
Your identification has been saved in wow_s1_rsa.
Your public key has been saved in wow_s1_rsa.pub.
The key fingerprint is:
SHA256:cciDNly/bIaYvquXhgw3L28SANM52BHLBzquuUNSZKQ root@b76ef9634888
The key's randomart image is:
+---[RSA 2048]----+
| ==+    .        |
|++Bo . + o       |
|E=o.. = = o      |
|..o. . + * .     |
| o .  o S =      |
|oo. +.   o       |
|=  + =..         |
|..  = *.         |
|..  .O+.         |
+----[SHA256]-----+
```

至此，生成秘钥对文件成功。

```sh
$ ls
wow_s1_rsa  wow_s1_rsa.pub
```

限制对私钥文件的访问，只有当前创建密钥对的用户才能读取，任何人都不能向其写入。

```sh
$ chmod 400 ~/.ssh/wow_s1_rsa
```



## 2、将公钥拷贝到远程机器主机

可使用 ssh-copy-id 命令将公钥拷贝到远程主机。

```sh
$ ssh-copy-id -i ~/.ssh/wow_s1_rsa.pub root@domain.com
```

`-i` 参数用户指定待拷贝的公钥文件。默认名称为 `id_rsa.pub`。

如果端口不是默认的 `22`，使 `-p` 参数指定。

命令执行成功后，公钥文件内容被附加到远程主机的  `~/.ssh/authorized_keys` 文件中，如果该文件在远程主机上不存在，会自动创建。

看到以下提示，输入 `yes` 回车

```sh
ECDSA key fingerprint is SHA256:Zb+HhA7riuYpuUcBhjQRtSw/0Uv3tyyV63rtZULjy78.
Are you sure you want to continue connecting (yes/no)?
```

输入密码，看到如下提示就表示成功了。

```sh
Number of key(s) added:        1
...
```



## 3、将私钥添加到 authentication agent

`authentication agent` 指的是 `ssh-agent` 高速缓存，`Mac` 上指的是本地钥匙串。

```sh
$ ssh-add -k ~/.ssh/wow_prod_rsa
```

提示：

```
Enter passphrase for /Users/ktgu/.ssh/wow_prod_rsa:
```

输入密码（注意这里是生成密钥对时自己设置的密码，不是远程主机的登陆密码），回车后看到如下信息表示从成功。

```sh
Identity added: /Users/ktgu/.ssh/wow_prod_rsa (ktgu@ktgus-mac.local)
```

如果提示错误，情况下面的 troubleshooting。



## 4、验证免密登陆

```sh
$ ssh root@domain.com
```



## 5、Troubleshooting

执行 `ssh-add` 时可能出现以下错误：

```sh
Could not open a connection to your authentication agent.
```

解决方案：

```sh
$ ssh-agent bash
```



（完）