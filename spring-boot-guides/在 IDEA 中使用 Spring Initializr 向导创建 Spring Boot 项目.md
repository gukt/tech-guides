

本文简单介绍如何利用 Intellij IDEA 的 Spring Initializr 向导创建 Spring Boot 单模块项目。

本文源代码：



## 利用 Spring Initializr 向导创建项目

首先，在 `IDEA` 中，依次点击 `File` -> `New` -> `Project` -> `Spring Initializr`：

<img src="https://raw.githubusercontent.com/gukt/images/master/github/images20200717184632.png" alt="image-20200717181602354" style="zoom:50%;" /> 

在 `Project SDK` 选择 `JDK 1.8`（如果没有设置过的话，点击 New... 新建一个 SDK 配置）。然后点击 `Next`，进入如下界面：

<img src="/Users/ktgu/Pictures/typora/image-20200717185440608.png" alt="image-20200717185440608" style="zoom:50%;" /> 

填写 `Group`，`Artifact`。这里填写的 Artifact 值对应写到 `settings.gradle` 文件中的 `rootProject.name` 属性中。

> Group 和 Artifact 是为了保证项目唯一性而提出的“坐标”的概念。
>
> Group - 项目组织的唯一标识符，对应项目的Packaging 包结构，一般分为多段，并使用倒序的公司/组织域名表示，比如：org.apache。
>
> Artifact - 项目的唯一标识符，实际对应 Project 的名称，也就是项目根目录的名称。比如：tomat。

`Package` 的值会自动使用 `Group` 和 `Artifact` 的值拼接而成，可以手动更改为自己认为更友好的名称。

接着选择 `Type`。选择基于 `maven` 还是 `gradle` 构建项目，选项有：

1. Maven Project, (Generate a Maven based project archive.)

2. Maven POM,  (Generate a Maven pom.xml.)

3. Gradle Project(Generate a Gradle based project archive.)

4. Gradle Config, (Generate a Gradle build file .)

如果选择 `#2` 或 `#4` ，创建项目后，仅创建 `pom.xml` 文件或 `build.gradle` 文件；而选择 `#1` 或 `#3`，会创建基于 `maven` 或 `gradle` 管理的项目结构，及相关文件。

我们使用 `Gradle` 且希望自动创建所需的项目结构，所以，`Type` 选择选项 `#3`。

`Language` 设置为 `Java`，可选项有：`Java`、`Kotlin`、`Groovy`。

`Packaging` 设置为 `Jar`，可选项有：`Jar`、`War`。

`Java Version` 我们选择 `8`。

`Name` 表示项目名称，会自动设置为和 `Artifact` 相同。

`Description` 是对项目的描述。

设置完成后，点击 `Next` 进入下一步，进入选择依赖界面：

<img src="https://raw.githubusercontent.com/gukt/images/master/github/images20200717184733.png" alt="image-20200717181707186" style="zoom: 50%;" /> 

该界面可以设置设置 `Spring Boot` 版本，以及项目的依赖（`Dependencies`），根据项目实际需要选择，然后，点击 `Next`。

<img src="/Users/ktgu/Pictures/typora/image-20200717185522691.png" alt="image-20200717185522691" style="zoom:50%;" /> 

`Project name` 和 `Module name` 默认被设置为之前设置的 `Artifact` 。

由于我们这里创建的是单模块项目，所以将 `Project name` 和 `Module name` 设置为相同.

`Project location` 表示项目所在根目录。

最后，`Project format` 有两个选项：`.idea (directory based)` 和 `.ipr (file based)`，选择默认的 `.idea`，表示将 IDEA 相关配置信息，放在项目根目录下的 `.idea` 目录中。

填写完成后，点击 `Finish` 完成。



## 2、项目结构及说明

初始化的项目结构如下：

<img src="/Users/ktgu/Pictures/typora/image-20200717185658401.png" alt="image-20200717185658401" style="zoom:50%;" /> 

因为我们选择的是使用 `gradle` 管理项目，所以向导会自动添加如下 gradle 相关的文件及目录：

- `build.gradle` - 项目依赖及构建相关配置，类似于 `maven` 的 `pom.xml` 。

- `settings.gradle`  - 项目名称（即 ArtifactId ）、模块名称相关配置，用于设置多模块项目，如果是单模块项目，该文件可以删除掉。

- `gradlew`、`gradlew.bat` - 它们分别对应 Linux下的 Shell 脚本、和 Windows 下的批处理文件，用以执行指定版本的 gradle。

  > gradlew 是 [gradle wrapper]() 的缩写，顾名思义就是对 gradle 命令的包装。
  >
  > 由于 gradle 版本迭代非常快，如果所有项目都依赖于环境 gradle，往往会出现很多问题。最好的办法是，项目可以指定依赖的 gradle 版本。于是就有了 gradle wrapper，它可以帮我们自动下载和配置项目依赖的指定版本的 gradle 。
  >
  > 可以在 gradle/wrapper/gradle-wrapper.properties 中设置版本。

- `gradle 目录` -  包含 `/wrapper/gradle-wrapper.properties` 和 `/wrapper/gradle-wrapper.jar` 两个文件，用以设置和执行 `gradle wrapper`（即上面的 `gradlew` 或 `gradlew.bat` 脚本 ）。

向导还帮我们自动添加了 `.gitignore` 文件，并将常用的忽略文件或目录都添加进去了，相当的人性化：

```
HELP.md
.gradle
build/
!gradle/wrapper/gradle-wrapper.jar
!**/src/main/**/build/
!**/src/test/**/build/

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr
out/
!**/src/main/**/out/
!**/src/test/**/out/

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/

### VS Code ###
.vscode/
```

`build.gradle` ：

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.1.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'java'
}

group = 'tech.codedog'
version = '0.1.0'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
}

test {
    useJUnitPlatform()
}

```

`settings.gradle` ：

```groovy
rootProject.name = 'sbg-spring-boot-scaffold'
```

`gradle/wrapper/gradle-wrapper.properties` ：

```properties
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.4.1-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```



## 运行 Spring Boot Application

运行应用程序：

```sh
$ gradle bootRun
```

如果之前还没有下载过指定的 `gradle` 版本，第一次运行时会先下载 `gradle`。

```properties
Connected to the target VM, address: '127.0.0.1:58537', transport: 'socket'

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.1.RELEASE)

2020-07-11 00:48:01.977  INFO 67991 --- [           main] t.c.sbggreeting.SbgGreetingApplication   : Starting SbgGreetingApplication on ktgus-mac.local with PID 67991 (/Users/ktgu/workspace/projects/spring-boot-guides/sbg-greeting/build/classes/java/main started by ktgu in /Users/ktgu/workspace/projects/spring-boot-guides/sbg-greeting)
2020-07-11 00:48:01.984  INFO 67991 --- [           main] t.c.sbggreeting.SbgGreetingApplication   : No active profile set, falling back to default profiles: default
2020-07-11 00:48:03.476  INFO 67991 --- [           main] t.c.sbggreeting.SbgGreetingApplication   : Started SbgGreetingApplication in 2.878 seconds (JVM running for 6.81)
Disconnected from the target VM, address: '127.0.0.1:58537', transport: 'socket'

Process finished with exit code 0
```

由于该项目没有添加任何依赖，所以启动成功后立即退出了应用程序。

如果要添加 Spring Boot 提供的各种 starter，请参考[官方文档](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)。



(完)

