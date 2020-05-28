本文简单介绍了如何将构建部署到远程主机，这里不赘述 jenkins 以及相关插件的安装。



## 1、生成 SSH 密钥对

由于我们需要使用 `ssh` 执行远程命令，因此我们需要生成密钥对，该密钥对给后面步骤中的 `ssh-agent` 插件使用。命令如下：

```sh
$ cd ~/.ssh
$ ssh-keygen -t rsa -f s1_rsa
```

需要输入 `passphrase` （记住这个，后面会用到），成功输入后回车。

此时我们可以看到.ssh 目录下多了两个文件:  `s1_rsa`、`s1_rsa.pub`



## 2、将公钥添加到远程主机

我们需要将公钥添加到远程主机

```sh
$ ssh-copy-id -i ~/.ssh/s1_rsa.pub root@s1.com
```

命令执行成功后，公钥会被附加到远程主机的 `~/.ssh/authorized_keys` 文件内容后。



## 3、在 jenkins 中添加凭据

接着我们需要添加一个类型为 `SSH username with private key` 的全局凭据。

步骤如下：

1. 打开 `jenkins -> 凭据 -> 系统` 
2. 点击`全局凭据`，点击`添加凭据`
3. 类型选择`SSH username with private key`
4. 在 `username` 中填写 `root`
5. 选中 `Enter directly`，点击 `Add`，将 `~/.ssh/s1_rsa` 文件内容复制粘贴进来。
6. 在 `Passphrase` 中输入之前创建密钥对时输入的密码。



## 4、配置 jenkins job

在“`构建环境`”中勾选 `SSH Agent`，然后在 `Credentials` 中选择“`指定凭据`”，然后在下拉框中选择前面添加的凭据。

![image-20200529014215684](https://raw.githubusercontent.com/gukt/images/master/github/imagesimage-20200529014215684.png)



现在就在 `执行 shell` 中利用 SSH 将本次构建部署到远程主机了。

```sh
imageName=gukt/wow-api
containerName=wow-api
remoteHost=root@wow233.com

#先将镜像推送到私有仓库
docker tag $imageName localhost:15000/$imageName
docker push localhost:15000/$imageName

#让远程主机从私有仓库获取本次构建的镜像
ssh $remoteHost "docker pull laogu.io:15000/$imageName:latest"

#让远程主机停止并删除旧的容器
ssh $remoteHost "docker rm -f $containerName | true"

#运行容器
ssh $remoteHost "docker run -d -p 8080:8080 -v /data/wow-api:/data/wow-api -e SPRING_PROFILES_ACTIVE=prod --name $containerName laogu.io:15000/${imageName}:latest"
```



(完)