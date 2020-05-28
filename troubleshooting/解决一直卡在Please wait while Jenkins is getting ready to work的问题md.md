打开 `jenkins` 的 `Updater Center` 配置文件

```sh
$ vim /data/jenkins/hudson.model.UpdateCenter.xml
```

将url修改为国内地址：https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

```xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>http://updates.jenkins-ci.org/update-center.json</url>
  </site>
</sites>
```

保存退出

```sh
:wq
```

重启jenkins容器

```sh
$ docker restart jenkins
```