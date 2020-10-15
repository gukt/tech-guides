搜索 redis 镜像，使用 stars 数最高的那个：

```sh
$ docker search redis
```

运行 redis 容器：

```sh
$ docker run \
	-d \
	--name redis \
	--restart always \
	-p 16379:6379 \
	redis:latest redis-server 
```

客户端连接测试：

```sh
$ docker exec -it redis redis-cli
```



（完）