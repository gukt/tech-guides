>  以下是以创建并运行 MariaDB 为例，如果想使用 MySQL，则将所有 MariaDB 改为 MySQL 即可



## 1、运行MariaDB容器

镜像：https://hub.docker.com/_/mariadb/

```bash
docker run \
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



## 2、连接MariaDB

```bash
docker exec -it mariadb mysql -uroot -p
输入密码
```



## 3、注意点

1. 映射data文件到宿主机的好处是随时可以删除容器再重建（docker rm -f mariadb)，数据一切都正常。
2. 如果想使用mysql，只需要将mariadb替换成mysql即可。



（完）