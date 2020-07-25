## 1、安装及卸载Java

查找已安装的 Java：

```sh
$ rpm -qa | grep java
java-1.7.0-openjdk-1.7.0.191-2.6.15.4.el6_10.x86_64
```

卸载之前安装的 Java：

```sh
$ yum -y remove java-1.7.0-openjdk-1.7.0.191-2.6.15.4.el6_10.x86_64
```

搜索要安装的版本

```sh
$ yum search java-1.8
```

安装指定的版本：

```sh
$ yum -y install java-1.8.0-openjdk.x86_64
```

验证安装成功：

```sh
$ java -version
```

在 Mac 上可以使用 dmg 文件安装，这里是官方[下载地址](https://www.oracle.com/java/technologies/javase-downloads.html)。



## 2、快速切换Jdk版本

打开配置文件：

```sh
$ vim ~/.bash_profile
```

添加如下内容：

```sh
#配置各种版本的JAVA_HOME
export JAVA_7_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home 
export JAVA_8_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home 
export JAVA_11_HOME=/Library/Java/JavaVirtualMachines/jdk-11.0.8.jdk/Contents/Home 
export JAVA_14_HOME=/Library/Java/JavaVirtualMachines/jdk-14.0.2.jdk/Contents/Home 

#设置别名命令，用于快速切换JAVA版本
alias jdk7='export JAVA_HOME=$JAVA_7_HOME' 
alias jdk8='export JAVA_HOME=$JAVA_8_HOME'
alias jdk11='export JAVA_HOME=$JAVA_11_HOME'
alias jdk14='export JAVA_HOME=$JAVA_14_HOME'

#设置默认JAVA_HOME为JDK7
jdk8
```

按 `:wq` 退出并保存。

让配置生效：

```sh
$ source ~/.bash_profile
```

切换版本：

```sh
$ jdk7 && java -version
Java(TM) SE Runtime Environment (build 1.7.0_80-ea-b05) 

$ jdk8 && java -version
Java(TM) SE Runtime Environment (build 1.8.0_51-b16) 
```



（完）