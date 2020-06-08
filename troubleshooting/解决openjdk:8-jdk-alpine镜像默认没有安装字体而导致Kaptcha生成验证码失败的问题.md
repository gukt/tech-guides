## 问题描述

当我们选择 `8-jdk-alpine` 作为运行 `java` 项目的镜像时，如果 java 项目中使用到 [Kaptcha](https://mvnrepository.com/artifact/com.github.penggle/kaptcha/2.3.2) 这个库来生成图形验证码，就会出现因为找不到字体而导致的异常。

这是因为 `8-jdk-alpine` 镜像中没默认有安装字体导致的。



## 解决方案

在 Dockerfile 文件中添加如下命令，安装必要的依赖：

```dockerfile
RUN apk add --no-cache ttf-dejavu fontconfig
```



（完）