## 设置项目使用 IDEA 编译，而不是 gradle 编译，以提高编译速度

打开 `Preferences` -> `Build, Execution, Deployment` -> `Build Tools` -> `Gradle`。

将 `Building and run using` 和 `Run tests using` 两处都改为 `Intellij IDEA`



## 使 lombok 生效，启用 Annotation Processing

打开 `Preferences` ，搜索 `Annotation Processors` ，选择 `Enable Annotation Processing` 



## 设置空的类或接口定义后面的花括号放在一行

打开 `Preferences` -> `Editor` -> `Code Style -Java` -> `Wraping and Bracces` 。

将 `Simple classes in one line` 的 checkbox 勾选上。



## 启用 SpringBoot 的 devtools

1. 打开 `build.gradle` 文件，在 `dependencies` 中添加依赖项：`compile("org.springframework.boot:spring-boot-devtools")`

2. 打开 `application.properties` ，设置两个开关

   ```properties
   spring.devtools.livereload.enabled=true
   spring.devtools.restart.enabled=true
   ```

3. 如果需要支持 `liverload` ，需下载并安装 `livereload` 插件，这样当app重启后，浏览器页面也会自动reload。

4. 打开 IDEA 的 `Preferences` -> `Build, Execution, Deployment` -> `Compiler` -> 将 `Build project automatically` 勾选上。

5. 在 IDEA 中输入快捷键 `shift+option+command+/` ，选择 `Registry...` ，勾选 `compiler.automake.allow.when.app.running`



## 创建代码作者，创建时间等信息的模板

打开 IDEA 的 `Preferences` -> `Editor` -> `File and live templates`，选择 `Includes` ，在 `File Header` 的右侧填写

```java
/**
 * @author https://github.com/gukt
 * @date  ${DATE} ${TIME}
 * @version 1.0
 */
```



## 阻止Unterminated statement警告

打开 `Pereference -> Editor -> Code Style -> Inspections`

搜索 `Unterminated statement` ，将对勾去掉。

![image-20200604143737459](https://raw.githubusercontent.com/gukt/images/master/github/imagesimage-20200604143737459.png)

