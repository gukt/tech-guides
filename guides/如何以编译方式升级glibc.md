## 1、安装gcc

```sh
yum install -y gcc
```



## 2、下载源文件

```sh
xz -d glibc-2.17.tar.xz 
tar -xvf glibc-2.17.tar 
cd glibc-2.17 
mkdir build 
cd build 
```



## 3、编译、安装

```sh
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
#安装
make && make install
...
Your new glibc installation seems to be ok.
make[1]: Leaving directory `/root/glibc-2.17'
```



## 4、查看是否安装成功

```sh
ldd --version
ldd (GNU libc) 2.17
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
由 Roland McGrath 和 Ulrich Drepper 编写。

Cheers!!!
```



（完）