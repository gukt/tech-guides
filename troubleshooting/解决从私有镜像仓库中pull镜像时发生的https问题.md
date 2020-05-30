### 问题描述

当执行 `docker pull laogu.io:15000/gukt/wow-api:latest` 时，抛出如下错误：

```sh
Error response from daemon: Get https://laogu.io:15000/v2/: http: server gave HTTP response to HTTPS client
```



### 解决方案

这是因为私有仓库并未提供 `https` 方式访问，而 `docker pull` 默认使用 `https` 访问仓库。

1. 创建或修改 `/etc/docker/daemon.json` 文件

   ```sh
   { "insecure-registries":["laogu.io:15000"] }
   ```

2. 重启 `docker` 守护进程

   ```sh
   $ sudo service docker restart
   ```

   

### 参考

1. https://github.com/docker/distribution/issues/1874

2. http://stackoverflow.com/questions/38695515/can-not-pull-push-images-after-update-docker-to-1-12

