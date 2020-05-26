免密登陆的原理是：先要在本机生成秘钥对，私钥放在本机，将公钥添加到远程服务器的 `~/.ssh/authorized_keys` 文件中。



## 1、生成密钥对

首先，在客户端利用 `ssh-keygen` 命令生成秘钥对。

```sh
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/ktgu/.ssh/id_rsa):
```

提示输入文件名，默认为 `id_rsa`，我们一般取一个方便识别的名称，比如：`github_rsa` 。

> 也可以指定 -f 参数指定密钥对名称： `ssh-keygen -t rsa -f github_rsa`

输入名称后回车，接着会提示连续两次输入密码，输入密码完成后回车，出现以下提示说明创建`秘钥对`成功了。

```sh
Your identification has been saved in wow_prod_rsa.
Your public key has been saved in wow_prod_rsa.pub.
The key fingerprint is:
SHA256:3QaqoYEzEQZTxvdlLzAwRw7aacPihpQXtRvm4q1iE+o ktgu@ktgus-mac.local
The key's randomart image is:
+---[RSA 2048]----+
|o+=.=+o          |
| +o=o*+ o        |
| o+o*=.= ..      |
|.o.=o.+ .o.o     |
|. *..o. S.. o    |
| o.ooo o   .     |
|. .....          |
|.+  .            |
|oEo.             |
+----[SHA256]-----+
```

成功后，会生成两个文件 `wow_prod_rsa`、`wow_prod_rsa.pub`（如果是首次生成秘钥对，还会多生成一个 `know_hosts` 文件）

限制对私钥文件的访问，只有您能读取此密钥，且任何人都不能向其写入。

```sh
$ chmod 400 ~/.ssh/wow_prod_rsa
```



## 2、将公钥拷贝到远程机器主机

如果端口不是默认的 22，使 `-p` 参数指定端口；如果要指定具体的文件名使用 `-i` 参数，默认文件名为 `id_rsa.pub`

```sh
$ ssh-copy-id -i ~/.ssh/wow_prod_rsa.pub root@domain.com
```

上面的`ssh-copy-id` 命令会将公钥文件附加到远程机器的 `~/.ssh/authorized_keys` 文件中（如果没有文件或目录，会在远程主机上自动创建）

看到以下提示，输入 `yes` 回车

```sh
ECDSA key fingerprint is SHA256:Zb+HhA7riuYpuUcBhjQRtSw/0Uv3tyyV63rtZULjy78.
Are you sure you want to continue connecting (yes/no)?
```

提示输入登陆远程主机的密码，输入密码后回车，看到如下提示就表示成功了。

```sh
Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'root@domain.com'"
and check to make sure that only the key(s) you wanted were added.
```



## 3、将私钥添加到 authentication agent

`authentication agent` 指的是 `ssh-agent` 高速缓存，MAC 上指的是本地钥匙串。

```sh
ssh-add -k ~/.ssh/wow_prod_rsa
```

提示：

```
Enter passphrase for /Users/ktgu/.ssh/wow_prod_rsa:
```

输入密码（注意这里是生成密钥对时自己设置的密码，不是远程主机的登陆密码），回车后看到如下信息表示从成功。

```sh
Identity added: /Users/ktgu/.ssh/wow_prod_rsa (ktgu@ktgus-mac.local)
```



## 4、验证免密登陆

```sh
$ ssh root@domain.com
```



## 5、Troubleshooting

执行ssh-add时可能出现以下错误：

```sh
Could not open a connection to your authentication agent.
```

解决方案：

```sh
ssh-agent bash
```



（完）