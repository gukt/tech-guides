注意：以下以创建并运行 mariadb 为例，如果想使用 mysql，只需将所有 mariadb 名称改为 mysql 即可。



# 运行 MariaDB 容器

其他版本镜像请见这里：https://hub.docker.com/_/mariadb/

```bash
$ docker run \
	--name mariadb \
	-p 13306:3306 \
	-v /data/mariadb/data:/var/lib/mysql \
  -v /data/mariadb/conf:/etc/mysql/conf.d \
	-e MYSQL_ROOT_PASSWORD=1234+aaaa \
	-e TZ=Asia/Shanghai \
	-d \
	mariadb:latest \
	--character_set_server=utf8mb4 \
	--collation_server=utf8mb4_unicode_ci
```



# 连接 MariaDB

```bash
$ docker exec -it mariadb mysql -uroot -p
# 输入密码
```



# 注意点

1. 映射 data 文件到宿主机的好处是随时可以删除容器再重建，数据一切都正常。
2. 如果想使用 mysql，只需要将上述命令中所有的 mariadb 名称替换成 mysql 即可。



（完）

