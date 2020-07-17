本文通过一个简单示例，介绍如何使用 gradle 构建 Java 应用程序并运行，其中包括：

1. 准备工作，安装 gradle，创建项目及代码结构。
2. 添加 java 插件，构建并生成可执行 jar 包。
3. 添加 application 插件，打包并运行应用程序。
4. 添加 war 插件，将项目打包成 war 交给容器运行。
5. build.gradle 常用的配置项。
6. 添加并管理依赖。
7. 使用 gradle wrapper 代替系统环境中的 gradle 进行构建。

本文源代码： https://github.com/gukt/spring-boot-guides/tree/master/sbg-gradle



## 准备工作

首先，我们需要在开发环境安装 `gradle`，请参考这篇[官方文档](https://docs.gradle.org/current/userguide/installation.html)。



### 创建项目及代码目录结构

创建项目根目录：

```sh
$ mkdir -p ~/projects/sbg-gradle
```

创建默认的代码目录：

```sh
$ cd ~/projects/sbg-gradle
$ mkdir -p src/main/java/tech/codedog/guides
```

在以上目录 `src/main/tech/java/codedog/guides` 中，`src/main/java` 是默认的源代码所在目录，`/tech/codedog/guides` 表示 `package` 目录。



### 写一段简单的代码

在这个 `src/main/java/tech/codedog/guides` 目录中，创建一个 `Hello.java` 文件：

```java
package tech.codedog.guides;

public class Hello {
  public static void main(String[] args) {
    System.out.println("Hello world");
  }
}
```



## 构建项目

要想编译和运行上面的代码，传统方式是使用 javac 命令， 这不是本文的重点，以下介绍如何使用 gradle 构建项目。



### 添加 java 插件

在项目根目录中，新建一个 `build.gradle` 文件，添加 [java](https://docs.gradle.org/current/userguide/java_plugin.html#header) 插件：

```groovy
plugins {
    id 'java'
}
```

添加了 `java` 插件后，运行 `gradle tasks` 会发现多了很多 `tasks` ：

```sh
$ gradle tasks

------------------------------------------------------------
Tasks runnable from root project
------------------------------------------------------------

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
buildDependents - Assembles and tests this project and all projects that depend on it.
buildNeeded - Assembles and tests this project and all projects it depends on.
classes - Assembles main classes.
clean - Deletes the build directory.
jar - Assembles a jar archive containing the main classes.
testClasses - Assembles test classes.

Build Setup tasks
-----------------
...

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.

Help tasks
----------
...

Verification tasks
------------------
check - Runs all checks.
test - Runs the unit tests.
```



### 编译 java 代码

这些 `tasks` 可以提供各种各样的用途，下面使用 `build task` 构建项目。

```sh
$ gradle build
```

构建完成后，会生成一个 `build` 目录，里面包含了本次构建产生的内容：

```
|____generated
| |____sources
| | |____annotationProcessor
| | | |____java
| | | | |____main
|____classes
| |____java
| | |____main
| | | |____tech
| | | | |____codedog
| | | | | |____guides
| | | | | | |____Hello.class
|____libs
| |____sbg-gradle-0.1.0.jar
|____tmp
| |____jar
| | |____MANIFEST.MF
| |____compileJava
```

`classes` 目录包含所有源代码文件被编译成的 `class` 文件。

`libs` 目录包含本次构建生成的 `jar` 文件。



### build.gradle 常用配置

```groovy
group 'tech.codedog'
version '0.1.0'
description 'How to build java with gradle'
archivesBaseName = 'sbg-gradlexxx'
sourceCompatibility = 8
targetCompatibility = 8
```

说明：

- `group` - 设置项目的 `GroupId`，注意: `ArtifactId` 是在 `settings.gradle` 文件中通过 `rootProject.name` 设置的。
- `version` - 设置项目的版本。
- `description` - 设置项目的描述信息。
- [archivesBaseName](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:archivesBaseName) - 设置打包文件的 base name，比如：打包生成 jar 文件时，如果没有在 jar 任务中特别指定，则默认使用这个属性值 。
- [sourceCompatibility](https://docs.oracle.com/en/java/javase/11/tools/javac.html#GUID-AEEC9F07-CB49-4E96-8BC7-BCC2C7F725C9)  - 指定可接受的源代码版本，8 和 1.8 等效，均表示 JDK 8。
- [targetCompatibility](https://docs.oracle.com/en/java/javase/11/tools/javac.html#GUID-AEEC9F07-CB49-4E96-8BC7-BCC2C7F725C9) - 指定编译的字节码运行的 JVM 版本（ Generates class files for a specific VM version.）



## 运行项目

运行项目有几种常用方式，这里介绍两种：

1. 将项目编译成可执行 `jar`。
2. 使用 `application` 插件，打包并生成启动脚本。



### 编译成可执行 `jar` 并运行

`java` 插件还提供了一个 `jar` 任务，它可以帮我们将项目打包成 `jar` 文件（ `build` 任务依赖这个 `jar` 任务）。

```sh
$ gradle jar
```

打包完成后，先尝试执行 jar 文件：

```sh
$ java -jar build/libs/sbg-gradle-0.1.0.jar
build/libs/sbg-gradle-0.1.0.jar中没有主清单属性
```

发生错误，这是因为没有指定 `Main-Class`，可以在 `build.gradle` 配置文件中的 `jar` 块中指定这个属性。

```groovy
jar {
 		manifest {
        attributes(
                'Main-Class': 'tech.codedog.guides.Hello'
        )
    } 
}
```

再次执行：

```sh
$ java -jar build/libs/sbg-gradle-0.1.0.jar
Hello world
```

显示 `Hello world` ，表示执行成功。



### 使用 `application` 插件

在 `build.gradle` 中添加 [aplication](https://docs.gradle.org/current/userguide/application_plugin.html) 插件：

```groovy
plugins {
    id 'application'
}
```

注意：`application` 插件隐式使用 `java` 插件，使用 `applicaiton` 插件后就可以去掉 `java` 插件了。

运行 `gradle tasks` 后，发现多了一些新的任务。

```sh
Application tasks
-----------------
run - Runs this project as a JVM application

Distribution tasks
------------------
assembleDist - Assembles the main distributions
distTar - Bundles the project as a distribution.
distZip - Bundles the project as a distribution.
installDist - Installs the project as a distribution as-is.
```



#### 使用 `gradle run` 运行应用程序

`run` 任务可以直接运行项目

```sh
$ gradle run
```

运行后，提示运行失败：

```sh
FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':run'.
> No main class specified and classpath is not an executable jar.
```

这是因为没有指定 `main class` 导致的， 需要在 `application` 块中设置：

```groovy
application {
    mainClassName = 'tech.codedog.guides.Hello'
}
```

再次启用应用程序：

```sh
$ gradle run

> Task :run
Hello world

BUILD SUCCESSFUL in 2s
```

还可以以调试模式启动：

```sh
$ gradle run --debug-jvm
```

如果要传入命令行参数，请使用 `--args`：

```sh
$ gradle run --args="foo=bar"
```



#### 打包项目，并使用生成的启动脚本运行应用程序

可以运行 `gradle installDist` 打包应用程序，`installDist` 任务依赖 `startScripts`，`startScripts` 可以生成开箱即用的 `Unix` 或 `Window` 平台启动脚本：

```sh
$ gradle installDist
> Task :compileJava
> Task :processResources NO-SOURCE
> Task :classes
> Task :jar
> Task :startScripts
> Task :installDist
```

执行成功后，执行脚本以及打包文件位于 `build/install` 目录下：

```
|____install
| |____sbg-gradle
| | |____bin
| | | |____sbg-gradle
| | | |____sbg-gradle.bat
| | |____lib
| | | |____sbg-gradle-0.1.0.jar
```

执行启动脚本：

```sh
$ build/install/sbg-gradle/bin/sbg-gradle
Hello world
```

另外，还可以使用 `gradle distZip` 或 `gradle distTar` 命令，将 `build/install/sbg-gradle` 中的全部文件打包成 `zip` 或 `tar` 包，并分发。

```sh
$ tar -tf build/distributions/sbg-gradle-0.1.0.tar
sbg-gradle-0.1.0/
sbg-gradle-0.1.0/lib/
sbg-gradle-0.1.0/lib/sbg-gradle-0.1.0.jar
sbg-gradle-0.1.0/bin/
sbg-gradle-0.1.0/bin/sbg-gradle
sbg-gradle-0.1.0/bin/sbg-gradle.bat
```

或者使用 `gradle assemble` 命令，同时执行 `distZip`、`distTar`。 



### 打包成 war 包，部署到容器中运行

如果希望将应用程序打成 war 包，并交由 tomcat 等容器运行，需使用 war 插件：

```groovy
plugins {
    id 'war'
}
```

然后执行 `gradle wa`r ，会生成 `build/libs/sbg-gradle-0.1.0.war` 文件，将其部署到容器中即可。



## 添加依赖

以上示例没有依赖任何的外部库，然而，大多数应用程序都是依赖外部库的，使用 gradle 可以轻松帮我们处理依赖，比如我们应用程序中要记录日志，希望引入 logback 库。

首先，改造一下 Hello.java 文件，以使用 logback 库输出一段日志：

```java
package tech.codedog.guides;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Hello {
  
  private static final Logger LOGGER = LoggerFactory.getLogger(Hello.class);
  
  public static void main(String[] args) {
    LOGGER.info("Hello world");
  }
}
```

然后，在 `build.gradle` 中添加：

```groovy
repositories {
    mavenCentral()
}
```

`respository` 块中定义要从哪个中央仓库中解决依赖，`mavenCentral` 仓库访问可能有点慢，可以添加阿里云仓库。

```groovy
repositories {
  	maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    mavenCentral()
}
```

然后在 `dependencies` 块中，添加第三方依赖：

```sh
dependencies {
    compile group: 'ch.qos.logback', name: 'logback-classic', version: '1.2.3'
    testCompile group: 'junit', name: 'junit', version: '4.12'
}
```

> 常用的查找第三方依赖的网站：https://mvnrepository.com/

运行 `gradle run` 查看输出：

```sh
$ gradle run
16:12:52.221 [main] INFO tech.codedog.guides.Hello - Hello world
```

在 `Hello.java` 中，由于将之前的 `System.out.println()` 输出日志改为了使用第三方日志库输出，所以看出打印的信息格式发生了变化。



## 使用 gradle wrapper 进行构建

在此之前，我们都是使用系统环境中的 gradle 进行构建的，但 gradle 迭代更新快，往往每个独立的项目会使用不同版本的 gradle，为了使每个项目可以灵活使用不同 gradle 版本，gradle 官方提供了 gradle wrapper 来构建项目。并推荐使用它。

在项目根目录，执行如下命令，添加 gradle wrapper 的支持：

```sh
$ gradle wrapper --gradle-version 6.5.1
```

执行后，项目根目录会新增如下目录及文件：

```sh
|____gradle
| |____wrapper
| | |____gradle-wrapper.jar
| | |____gradle-wrapper.properties
|____gradlew
|____build.gradle
|____gradlew.bat
```

新增的批处理脚本（gradlew 用于 Linux 系统； gradlew.bat 用于 Windows 系统），用以启动 Gradle Wrapper。

```sh
$ ./gradlew build
```

如果要更改 Gradle Wrapper 使用的 `gradle` 版本，可在 `gradle-wrapper.properties` 中修改

```properties
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.5.1-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```



## 参考

1. [Building Java Projects with Gradle](https://spring.io/guides/gs/gradle/)
2. [The Application Plugin for gradle](https://docs.gradle.org/current/userguide/application_plugin.html#header)
3. [The Java Plugin for Gradle](https://docs.gradle.org/current/userguide/java_plugin.html#header)
4. https://docs.gradle.org/current/dsl/org.gradle.api.Project.html

