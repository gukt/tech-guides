## 原因

域名被DNS污染了。



## 解决方案

进入[这个网站](https://site.ip138.com/raw.Githubusercontent.com/)查看域名被解析到的地址。

找一个ping 值最低的 IP 地址，比如 `151.101.76.133 中国 香港`

修改 `/etc/hosts` 文件。

```sh
$ sudo vim /etc/hosts
```

添加如下行：

```sh
151.101.76.133 raw.githubusercontent.com
```

保存，现在就可以访问了。



（完）

