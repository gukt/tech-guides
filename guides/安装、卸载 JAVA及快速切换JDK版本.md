## 安装及卸载Java

```sh
#查找已安装的java
rpm -qa | grep java
java-1.7.0-openjdk-1.7.0.191-2.6.15.4.el6_10.x86_64

#卸载之前安装的JDK1.7
yum -y remove java-1.7.0-openjdk-1.7.0.191-2.6.15.4.el6_10.x86_64

#搜索要安装JDK1.8版本
yum search java-1.8
#安装指定的版本
yum -y install java-1.8.0-openjdk.x86_64
#验证安装成功
java -version
```



## 快速切换Jdk版本

```sh
#打开配置文件
vim ~/.bash_profile

#配置各种版本的JAVA_HOME
export JAVA_7_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home 
export JAVA_8_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home 

#设置默认JAVA_HOME为JDK7
export JAVA_HOME=$JAVA_7_HOME 

#设置别名命令，用于快速切换JAVA版本
alias jdk7='export JAVA_HOME=$JAVA_7_HOME' 
alias jdk8='export JAVA_HOME=$JAVA_8_HOME' 

#让配置生效
source ~/.bash_profile

#切换版本测试
jdk7 && java -version
Java(TM) SE Runtime Environment (build 1.7.0_80-ea-b05) 

jdk8 && java -version
Java(TM) SE Runtime Environment (build 1.8.0_51-b16) 

```

