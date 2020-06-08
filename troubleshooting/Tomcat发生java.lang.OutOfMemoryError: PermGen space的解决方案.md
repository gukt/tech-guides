## 解决方案

产生该问题的主要原因是JVM永久带空间不足导致的，可以在环境变量CATALINA_OPTS中提高MaxPermSize参数值

```sh
set CATALINA_OPTS = -XX:PermSize=128m -XX:MaxPermSize=512m
```

如果是堆栈溢出,则需要提高xms的值

```sh
set CATALINA_OPTS = -Xms128m -Xmx1024m -XX:PermSize=128m -XX:MaxPermSize=512m
```

参数解释:

> -xms128：表示JVM初始堆栈大小
> -xmx1024：表示JVM堆栈大小最大可以达到1M内存
> -XX:PermSize=128m：表示初始永久带内存大小
> -XX:MaxPermSize=512m：表示永久带最大内存大小



## 为什么设置CATALINA_OPTS就会生效？

因为在tomcat启动时会使用环境变量：JAVA_OPTS，CATALINA_OPTS, DEBUG_OPTS指定的值，查看%CATALINA_HOME%/bin/catalina.sh文件可以看到如下的代码段：

```sh
%_EXECJAVA% %JAVA_OPTS% %CATALINA_OPTS% %DEBUG_OPTS% -Djava.endorsed.dirs="%JAVA_ENDORSED_DIRS%" -classpath "%CLASSPATH%" -Dcatalina.base="%CATALINA_BASE%" -Dcatalina.home="%CATALINA_HOME%" -Djava.io.tmpdir="%CATALINA_TMPDIR%" %MAINCLASS% %CMD_LINE_ARGS% %ACTION%
```

