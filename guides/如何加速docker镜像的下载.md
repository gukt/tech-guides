很多时候下载 docker image 速度慢的像蜗牛，我们可以更改 docker image 的下载地址为国内源，以加速下载。

国内较快的下载源有：

1. docker 官方中国区 - https://registry.docker-cn.com
2. 网易 - http://hub-mirror.c.163.com
3. 中科大 - https://docker.mirrors.ustc.edu.cn
4. 自己在阿里云申请加速地址



## 1、设置

首先查看当前下载源是什么？

```sh
$ docker info
...
Registry: https://index.docker.io/v1/
...
```

输出内容中的 `Registry` 就是当前 image 的下载地址。

打开 `/etc/docker/daemon.json` 文件（该文件也可能没有，没有就创建一个）

```sh
$ vim /etc/docker/daemon.json
```

在 registry-mirrors 中添加新的下载地址（如果文件没有就将以下整个内容粘贴进新文件）。

```
{
  "registry-mirrors": [
      "https://docker.mirrors.ustc.edu.cn"
  ]
}
```

保存退出

```sh
:wq
```



## 2、重启服务

```sh
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```



## 3、检查是否设置成功

```sh
$ docker info
```

如果输出结果中包含如下内容表示设置成功：

```
...
 Registry Mirrors:
  https://docker.mirrors.ustc.edu.cn/
...
```

以后下载 docker image 速度就会飞快了。



（完）