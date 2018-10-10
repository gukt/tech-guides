#### 配置Remote debug configuration

打开IDE -> Run -> Edit Configurations，点击“+“，选择”Remote"，出现如下界面：

![image-20181010224422972](/Users/ktgu/Library/Application Support/typora-user-images/image-20181010224422972.png)



Debugger mode 选择： `Attach to remote JVM`

Host填写远程服务器地址，

Port填写远程服务器监听端口

> Command line arguments for remote JVM说明：
>
> -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005
>
> 以上这段参数是要放到远程服务器进程启动命令行

保存退出！



#### 启动远程进程

```
java -jar -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005 /data/nn_game/game-server.jar
```



#### 启动本地调试

启动之前配置的Remote debug configuration,当控制台出现如下信息时表示本地调试进程已经连接到远程

```
Connected to the target VM, address: 'nn.laogu.io:5005', transport: 'socket'
```

