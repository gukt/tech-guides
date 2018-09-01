### 查看端口占用情况

```sh
[root@biz1 ~]# netstat -tulpen
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode      PID/Program name
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      1000       46174      5179/mysqld
tcp        0      0 127.0.0.1:11211         0.0.0.0:*               LISTEN      1003       13189      995/memcached
tcp        0      0 127.0.0.1:6379          0.0.0.0:*               LISTEN      1002       13927      
```

```
tgus-mac:server ktgu$  lsof -i tcp:13301
COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
java    70067 ktgu  219u  IPv6 0x60e648c40af24f2d      0t0  TCP *:13301 (LISTEN)
```



### 添加软连接，指向目录

```sh
#添加软连接：ln -s 源文件 目标文件 (目标目录下的同名文件夹 -> 源文件)
ln -s /usr/local/mycms/upload/ /usr/local/mycms/webapps/mycms/WEB-INF/static/
```



### Tomcat 8允许访问软连接目录下的文件

修改conf/context.xml：

```xml
<!-- Tomcat 7: -->
<Context allowLinking="true" />
 
<!-- Tomcat 8: -->
<Context>
  <Resources allowLinking="true" />
</Context>
```

修改后重启tomcat!



### SSH连接保持时间太短的问题

修改心跳间隔以及相关参数

```sh
#打开/etc/ssh/sshd_config文件
vim /etc/ssh/sshd_config

#找到以下两行，去掉注释
#ClientAliveInterval:表示发送心跳的间隔
#ClientAliveCountMax:最大允许多少次心跳没响应
ClientAliveInterval 60
ClientAliveCountMax 3
#保存后退出

#重启sshd
service sshd restart
```



### 安装jenkins

```sh
#下载yum源，安装yum源，然后使用yum安装jenkins
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins

#启动jenkins服务
systemctl start jenkins

#打开浏览器,访问：http://localhost:8080

#查看初始密码
cat /var/lib/jenkins/secrets/initialAdminPassword

#以root用户运行jenkins
vim /etc/sysconfig/jenkins
JENKINS_USER="root"
#重启jenkins
systemctl restart jenkins
```



### 安装subversion，并添加多个仓库

```sh
#yum安装subversion
yum install subversion
#检查版本
svnserve --version
#新建REPOS_PATH目录
mkdir -p /data/svn
#分别创建proj-1, proj-2两个仓库
svnadmin create /data/svn/proj-1/
svnadmin create /data/svn/proj-2/

#查看内容
ll /data/svn/proj-1/
总用量 24
drwxr-xr-x 2 root root 4096 7月   7 01:49 conf
drwxr-sr-x 6 root root 4096 7月   7 01:49 db
-r--r--r-- 1 root root    2 7月   7 01:49 format
drwxr-xr-x 2 root root 4096 7月   7 01:49 hooks
drwxr-xr-x 2 root root 4096 7月   7 01:49 locks
-rw-r--r-- 1 root root  229 7月   7 01:49 README.txt

#将proj-1/conf/下的authz,passwd文件移到../../目录
cd /data/svn/proj-1/conf; mv authz passwod ../../

#修改proj-1的svnserver.conf
vim conf/svnserve.conf
#将以下行的注释去掉后保存
anon-access = read
auth-access = write 
password-db = ../../passwd
authz-db = ../../authz

#同样地，将proj-2/conf/svnserve.conf内容保持和proj-1一致，并删除proj-2/conf下的authz,passwd文件

#配置访问用户及密码
vim /data/svn/passwd

#添加用户，并保存
gukt = aaa

#配置授权,配置dev组，并给dev group授权rw,然后保存
vim /data/svn/authz
dev = gukt
[/]
@dev = rw

#启动svn服务
svnserve -d -r /data/svn/

#使用svn客户端命令测试
svn co svn://127.0.0.1/proj-1
认证领域: <svn://127.0.0.1:3690> 98d69ce0-d0d0-499d-8ee6-67ea4caa9f28
“root”的密码:
认证领域: <svn://127.0.0.1:3690> 98d69ce0-d0d0-499d-8ee6-67ea4caa9f28
用户名: gukt
“gukt”的密码:

-----------------------------------------------------------------------
注意!  你的密码，对于认证域:

   <svn://127.0.0.1:3690> 98d69ce0-d0d0-499d-8ee6-67ea4caa9f28

只能明文保存在磁盘上!  如果可能的话，请考虑配置你的系统，让 Subversion
可以保存加密后的密码。请参阅文档以获得详细信息。

你可以通过在“/root/.subversion/servers”中设置选项“store-plaintext-passwords”为“yes”或“no”，
来避免再次出现此警告。
-----------------------------------------------------------------------
保存未加密的密码(yes/no)?yes
取出版本 0。

#至此，svn中配置多个项目已经完成
```



### 解决SVN客户端版本不匹配问题

**问题描述：**

```
Can't use Subversion command line client: /usr/local/bin/svn
         Subversion command line client version is too old (1.10.0). Fix it.
```

**问题说明：**

应该是IDE的提示错误，不是`too old`,而是`too new`

因为svnserve版本号是1.8，而本地安装的svn客户端版本号是1.10，从IDEA的`Version Cotnrol`标签的`Subversion working copies information` 标签中也可以看得出源代码的版本：

```
/Users/ktgu/github/mycms
URL:	svn://laogu.io/yufo/codes/trunk
Format:	1.8
Depth:	infinity
```

**解决方案：**

重新安装与源代码格式版本匹配的客户端(以下例子是在mac环境中)

```
brew install subversion@1.8
```

安装完成后，打开IDEA的`Settings` - > `Verson Control` -> `Subversion` -> `General` -> `Use command line client`, 选择刚刚安装的svn命令即可（安装的1.8版本的svn命令所在地址：`/usr/local/Cellar/subversion@1.8/1.8.19/bin/svn`）



### 开启启动脚本添加启动mysql，svn等

```sh
vim /etc/rc.local

#启动mysqld服务
service mysqld start

#启动svn服务器
svnserve -d -r /data/svn/

```



### Yum安装nodejsV10.x

```sh
#安装源
curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -

#yum安装
yum -y install nodejs

#检查是否安装成功
node -v
npm -v
```



### 配置SSH免密码登录

```sh
#客户端：进入~/.ssh目录，如果没有则创建一个
mkdir ~/.ssh; cd ~/.ssh

#客户端：生成rsa秘钥
ssh-keygen -t rsa

#生成后，在.ssh目录将产生三个文件：id_rsa,id_rsa.pub,know_hosts
#id_rsa：存储私钥
#id_rsa.pub:存储公钥

#客户端：将公钥拷贝到云主机
scp ~/.ssh/id_rsa.pub root@12.34.56.78:/root/.ssh/id_rsa.pub
#NOTE：如果云主机上没有.ssh目录，则需要先登陆手动创建一个

#客户端：登陆进云主机
ssh root@12.34.56.78

#服务端：将客户端公钥放入authorized_keys文件夹中，并销毁公钥
cd ~/.ssh; cat id_rsa.pub >> authorized_keys; rm -f id_rsa.pub

#客户端：验证是否成功
ssh root@12.34.56.78

#NOTE: 生成rsa秘钥时，要将密码留空，否则以后每次登陆仍然需要提示输入密码
```



### 启动Jenkins@mac

```sh
#查看本机安装的jenkins相关信息
brew info jenk

#启动jenkins服务
brew services start jenkins

#启动完成后，查看进程
ps aux|grep jenkins
ktgu             98760  10.5 11.1  8055320 934564   ??  S    12:55上午   0:52.98 /usr/bin/java -Dmail.smtp.starttls.enable=true -jar /usr/local/opt/jenkins/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=8080

#重启jenkins
brew services restart jenkins

#显示所有服务
brew services list
```



### 将本地更改提交到远程仓库

```sh
#先创建远程仓库(假设为my-notes)
#创建好后，克隆到本地
git clone https://github.com/gukt/my-notes.git

#在当前执行命令的目录生成了一个my-notes的目录
#将文件添加到my-notes目录或对其中的文件进行更改
#添加所有新增的文件到本地仓库
git add .

#查看当前状态
git status

#提交更改
git commit -m 'init import'

#push到远程仓库
git push
```



### 快速切换Jdk版本

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



