## 1、安装前准备

首先要安装后面编译代码时所需的依赖

```bash
$ yum install -y make autoconf zlib-devel xmlto  perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker
```

卸载老版本 git

```bash
$ yum remove git
```

下载新版本的源代码

```bash
$ cd /usr/local
$ wget https://github.com/git/git/archive/v2.9.2.tar.gz
```

解压并进入解压后的目录

```bash
$ tar xvf v2.9.2.tar.gz
$ cd git-2.9.2
```



## 2、编译，安装

```sh
$ make configure
$ ./configure --prefix=/usr/local --with-iconv=/usr/local/libiconv
$ make all doc
$ make install install-doc install-html
```

修改PATH环境变量，添加指向/usr/local/git/bin目录

```bash
$ vim /etc/profile
#在末尾追加以下内容
$ export PATH=/usr/local/git/bin:$PATH
#保存退出
#使更改立即生效
$ source /etc/profile
```



## 3、检查是否安装成功

```bash
$ git version
$ git version 2.9.2
```



## 4、删除安装文件

```bash
$ rm -rf v2.9.2.tar.gz
```

