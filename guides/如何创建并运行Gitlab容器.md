详细安装及说明请参考官方文档：https://docs.gitlab.com/omnibus/docker/README.html



## 1、运行容器

```sh
$ docker run \
	-d \
  -p 10001:10001 \
  --restart always \
  --volume /data/gitlab/config:/etc/gitlab:Z \
  --volume /data/gitlab/logs:/var/log/gitlab:Z \
  --volume /data/gitlab/data:/var/opt/gitlab:Z \
  --name gitlab \
  gitlab/gitlab-ce:12.3.9-ce.0
#  gitlab/gitlab-ce:latest
```

gitlab 的数据存于宿主机的 `/data/gitlab/` 目录中，系统重启后容器自动重启。

启动 gitlab 容器非常慢，可以查看日志看启动进度：

```sh
$ docker logs -f gitlab
```



## 安装中文版

```sh
$  
```



## 2、测试验证

现在，可以通过 http://localhost:10001（或域名）访问容器中启动的 gitlab。

要求我们输入新密码，输入后点击 "`change your password`" 按钮，这就是 root 的密码。

修改成功后使用 `root` 用户名和刚刚输入的新密码登陆，成功后将调整到 "`Welcome to GitLab`" 页



（完）