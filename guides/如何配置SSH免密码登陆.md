免密登陆的原理是：先要在本机生成秘钥对，公钥给远程服务器，私钥放在本机，然后将公钥添加到远程服务器的`~/.ssh/authorized_keys`文件中。



## 1、执行以下命令

```sh
#客户端：生成rsa公钥-私钥对
#执行命令过程中会提示输入要保存到的文件名，默认is_rsa
#接着会提示输入密码
#执行完成会在~/.ssh目录将产生三个文件：id_rsa,id_rsa.pub,know_hosts
#id_rsa存储私钥,id_rsa.pub存储公钥
#NOTE: 如果不指定-f参数，名称默认也是id_rsa
$ ssh-keygen -t rsa -f ~/.ssh/id_rsa

#限制对您的私钥的访问，只有您能读取此密钥，且任何人都不能向其写入
$ chmod 400 ~/.ssh/id_rsa

#将公钥拷贝到远程机器
#如果端口不是默认的22，使-p指定端口
#如果要指定具体的文件名使用-i参数，默认文件名为id_rsa.pub
#ssh-copy-id命令会将公钥附加到远程机器的~/.ssh/authorized_keys文件中（如果没有文件或目录的话会自动创建）
$ ssh-copy-id -i ~/.ssh/id_rsa.pub root@domain.com

#还需要将本地私钥添加到'authentication agent'(ssh-agent的高速缓存，MAC上指的是本地钥匙串)
#adds private key identities to the authentication agent
#不然会提示：Enter passphrase for key '/Users/ktgu/.ssh/id_rsa'
$ ssh-add -k ~/.ssh/id_rsa
#每个服务器的私钥文件是不一样的，以下添加google cloud的私钥文件到authentication agent
#$ ssh-add -k ~/.ssh/gcloud_rsa
#NOTE: 若出现'Could not open a connection to your authentication agent.'错误，请执行：
$ ssh-agent bash

#至此，就可以免密登陆远程主机了
#验证是否成功
$ ssh root@domain.com
```



## 2、Troubleshoting

执行ssh-add时可能出现以下错误：

```sh
Could not open a connection to your authentication agent.
```

解决方案：

```sh
ssh-agent bash
```

