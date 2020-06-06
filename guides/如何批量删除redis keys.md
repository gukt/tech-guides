## 1、批量删除 redis keys（本地）

使用如下命令可以删除本地 redis 服务器中指定 pattern 的 keys：

```sh
$ redis-cli keys cache* | xargs ./redis-cli del
```



## 2、批量删除 redis keys（远程）

如果要删除远程 redis 服务器中的 keys，其实原理一样，唯一不同的是 redis-cli 命令要提供 `-h` 参数。如果redis 端口不是使用的默认端口，还需要提供 -p 参数，命令如下：

```sh
$ redis-cli -h s1 -p 16379 keys cache* | xargs redis-cli -h s1 -p 16379 del
```



## 3、验证是否删除成功

```sh
$ redis-cli -h s1 -p 16379
```

进入控制台后，输入：

```redis
s1:16379> keys cache*
(empty list or set)
```



（完）







