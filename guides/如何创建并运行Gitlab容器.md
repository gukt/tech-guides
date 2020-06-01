详细安装及说明请参考官方文档：https://docs.gitlab.com/omnibus/docker/README.html



## 1、运行容器

```sh
$ docker run \
	-d \
  --hostname git.laogu.io \
  -p 10080:80 \
  --restart always \
  --volume /data/gitlab/config:/etc/gitlab:Z \
  --volume /data/gitlab/logs:/var/log/gitlab:Z \
  --volume /data/gitlab/data:/var/opt/gitlab:Z \
  --name gitlab \
  gitlab/gitlab-ce:latest
```

gitlab的数据存于宿主机的`/srv/gitlab/`目录中，系统重启后容器自动重启。

> 启动容器需要一小段时间，如果先查看容器启动日志使用如下命令：
>
> `sudo docker logs -f gitlab`



## 2、测试验证

现在，可以通过http://localhost:10001（或域名）访问容器中启动的gitlab

呈现的网页中要求我们输入新密码，输入后点击"change your password"按钮。

修改成功后使用root用户名和刚刚输入的新密码登陆，成功后将调整到"Welcome to GitLab"页



（完）