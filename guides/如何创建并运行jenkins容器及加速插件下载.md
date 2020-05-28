如果是全新安装，考虑是否要将旧版本映射的目录删除掉

```sh
$ rm -rf /data/jenkins
```



## 1、启动容器

关于jenkins 的详细安装及说明请参考官方文档：https://jenkins.io/zh/doc/book/installing/#docker。

运行 jenkins 容器：

```
#启动容器
docker run \
  -u root \
  -p 10000:8080 \
  -p 50000:50000 \
  -v /data/jenkins:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name jenkins \
  -d \
  jenkinsci/blueocean
```

说明：

官网推荐使用jenkinsci/blueocean镜像，该镜像包含当前的长期支持 (LTS) 的Jenkins版本 （可以投入使用） ，捆绑了所有Blue Ocean插件和功能。这意味着你不需要单独安装Blue Ocean插件。

当然了，也可以使用其他镜像：https://hub.docker.com/r/jenkins/jenkins/，这个是基于LTS版本的每周更新，

> 注意：标记official的那个镜像（https://hub.docker.com/_/jenkins）已经deprecated（被废弃）了。

5000端口与JNLP有关，请见：https://github.com/jenkinsci/docker/blob/master/README.md



## 2、访问 jenkins 

容器成功运行后，我们就可以在浏览器中输入`浏览：http://localhost:10000`进行登录了，此时会出现"解锁Jenkins"界面出现。

我们可以通过如下命令获得初始密码：

```sh
$ docker logs jenkins
...
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

59e6a6c810014d3aa8ac83cc27e6e38d

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
...
```

输出结果中的 59e6a6c810014d3aa8ac83cc27e6e38d 就是初始密码。

也可以查看jenkins_home下的/secrets/initialAdminPassword文件内容，由于容器的jenkins_home目录已经映射到/data/jenkins目录了，所以可以在宿主机上直接查看

```sh
cat /data/nginx/secrets/initialAdminPassword
```



## 3、加速Jenkins插件下载

第一种方式：

```sh
# 打开配置文件
vim /data/jenkins/updates/default.json

# 替换所有插件下载的url
1,$s/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g

# 替换连接测试url
1,$s/http:\/\/www.google.com/https:\/\/www.baidu.com/g

#保存退出
:wq

#重启jenkins容器
docker restart jenkins
```

第二种方式：

```sh
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' /data/jenkins/updates/default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' /data/jenkins/updates/default.json

#重启jenkins容器
docker restart jenkins
```



（完）

 