打开配置文件 `~/.vimrc`（修改该文件仅影响当前用户）也可以修改全局配置：`/etc/vimrc`

```sh
$ vim ~/.vimrc
```

在文件末尾加上如下配置项

```properties
#设置tab的值为2个空格，默认为8个空格
set ts=2
#输入tab时自动将其转化为空格
set expandtab
#回车后自动缩进
set autoindent
```

保存退出

```sh
:wq
```

