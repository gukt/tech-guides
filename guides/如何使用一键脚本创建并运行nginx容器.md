为了方便维护和管理，我们需要将容器内的三个重要文件映射到宿主机目录，nginx 容器内目录情况如下：

1. `/usr/share/nginx/www` - 该目录保存网站的静态文件。

2. `/etc/nginx` - 该目录保存所有配置文件。

3. `/var/log/nginx` - 日志文件。

     

## 1、创建并运行容器

以下提供一键脚本运行 `nginx` 容器。

> 注意：该脚本同时适用于“自定义镜像（ [gukt/nginx:alpine](https://github.com/gukt/docker-images/blob/master/nginx/alpine/Dockerfile) ）”和“官方镜像（ [nginx:alpine](https://hub.docker.com/layers/nginx/library/nginx/alpine/images/sha256-833d1e074ee086184af1bae1c4cc68aa26ce518588001f84c33164f7a66274b4?context=explore) ）”

如果要使用自定义镜像，请使用如下命令进行编译 `image` :

```sh
$ mkdir -p docker-images/nginx
$ cd docker-images/nginx
$ wget --no-check-certificate -v https://raw.githubusercontent.com/gukt/docker-images/master/nginx/alpine/Dockerfile
$ docker build -t gukt/nginx:alpine .
```

在容器中运行 `nginx` 的一键脚本：

```bash
$ set +x \
	&& imageName=gukt/nginx:alpine \
	&& containerName=nginx \
	&& defaultHttpPort=10080 \
	&& echo 'step1: 准备工作：创建临时容器并拷贝配置文件' \
	&& echo "正在备份旧的映射目录..." \
	&& mkdir -p /data/backup \
	&& cp -R /data/$containerName /data/backup/$containerName-$(date +"%Y-%m-%d-%H-%M-%S" ) | true \
	&& echo '正在准备空的映射目录...' \
	&& rm -rf /data/$containerName | true; mkdir -p /data/$containerName \
	&& echo '正在尝试删除旧的tmp-nginx容器（如果没有则继续执行）...' \
	&& docker rm -f tmp-nginx | true \
	&& echo '运行nginx临时容器...' \
	&& docker run --name tmp-nginx -d $imageName \
	&& echo '从容器中拷贝所有配置文件到本地映射目录...' \
	&& docker cp tmp-nginx:/etc/nginx /data/$containerName/conf \
	&& docker cp tmp-nginx:/usr/share/nginx/html /data/$containerName/html \
	&& echo 'It works.' > /data/$containerName/html/index.html \
	&& echo '删除临时容器...' \
	&& docker rm -f tmp-nginx \
	&& echo Done. \
	&& echo '======================' \
	&& echo 'step2: 运行正式容器...' \
	&& echo '正在尝试删除旧的nginx容器（如果没有则继续执行）...' \
	&& docker rm -f $containerName | true \
	&& echo '开始创建正式容器...' \
	&& docker run \
       -d \
       -p $defaultHttpPort:80 \
       -p 443:443 \
       -p 10086:86 \
       --name $containerName \
       -v /data/$containerName/html:/usr/share/nginx/html \
       -v /data/$containerName/conf:/etc/nginx \
       -v /data/$containerName/logs:/var/log/nginx \
       $imageName \
  && echo Done.
```

> **NOTE**
>
> 1. 利用 `docker` 拷贝文件时，父路径必须提前存在。
> 2. 如果要使用官方镜像，请将 `imageName` 变量设置为 [nginx:alpine](https://hub.docker.com/layers/nginx/library/nginx/alpine/images/sha256-833d1e074ee086184af1bae1c4cc68aa26ce518588001f84c33164f7a66274b4?context=explore) 。
> 3. 执行 `cp` 命令时，`tmp-nginx:/etc/nginx`后面不要加`/`，否则 `nginx` 目录会作为 `/data/nginx/conf` 的子目录，而非拷贝目录下的所有文件。



## 2、测试是否正常工作

```sh
#在 host 机器上运行,显示 'It works' 表示 nginx 已正常工作
$ curl localhost:10080
It works
```



## 3、重新加载nginx配置

可直接在宿主机上以交互方式运行容器中的 `nginx -s reload` 命令：

```sh
$ docker exec -it nginx nginx -s reload
```

如果要重新载入 `Nginx` 可以使用以下命令发送 `HUP` 信号

```sh
$ docker kill -s HUP nginx
```



## 4、参考

[^1]:https://github.com/gukt/docker-images/blob/master/nginx/alpine/Dockerfile 
[^2]: https://hub.docker.com/_/nginx



（完）