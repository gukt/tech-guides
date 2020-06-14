详细安装及细节请参考官方文档：https://docs.docker.com/install/linux/docker-ce/centos/

有多种方式可以安装docker，此处仅介绍yum方式安装docker。



## 1、安装必要的依赖

在安装 docker-ce之前，首先要安装必要的依赖。

```sh
$ sudo yum install -y \
  yum-utils \
  device-mapper-persistent-data \
  lvm2
```

> yum-utils provides the yum-config-manager utility,
>
> device-mapper-persistent-data and lvm2 are required by the devicemapper storage driver



## 2、添加docker-ce仓库

yum默认仓库中不包含`docker-ce`、`docker-ce-cli`、`containerd.io`，所以需要使用  `yum-config-manager` 命令添加一个可用的仓库。因为docker官方源（https://download.docker.com/linux/centos/docker-ce.repo）下注速度比较慢，所以以下使用国内源。

```sh
$ sudo yum-config-manager \
    --add-repo \
    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```

NOTE: 也可以选择阿里云镜像（http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo）



## 3、开始安装

安装最新版本的`docker-ce`、`docker-ce-cli`、`containerd.io`

```sh
$ sudo yum install -y docker-ce docker-ce-cli containerd.io
```



## 4、启动 docker

```sh
$ sudo systemctl start docker
```



## 5、设置开机自动启动

```sh
$ sudo systemctl enable docker
```



## 6、查看docker版本

 ```sh
$ docker version
 ```



（完）

