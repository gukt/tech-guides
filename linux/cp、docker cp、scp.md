# 1、linux cp 命令

`cp` 命令用以拷贝一个或多个源文件（或目录）到目标文件夹。这是一个基本且常用的命令，务必要熟练掌握。



## 用法及常用选型

cp 命令的用法如下：

```
cp [选项]... [-T] 源文件 目标文件
cp [选项]... 源文件... 目录
cp [选项]... -t 目录 源文件...
```

常用的选型有：

- -a：复制时，保留文件的结构和属性
- -i：覆盖文件时会有提示
- -p：--preserve=mode,ownership,timetemp，保存文件的部分属性
- -r，-R：均表示递归
- -v：显示过程



## 复制行为总结

- 如果 `SRC` 是单个文件：

  - 如果 `DEST` 存在：
    - 是文件：使用源文件内容覆盖目标文件
    - 是目录：复制源文件到 `DEST` 中，文件名保持不变。
  - 如果 `DEST` 不存在：
    - 如果父路径不存在：一律报错
    - 如果父路径存在：
      - 如果 DEST 以 / 结尾，报错
      - 如果 DEST 不以 / 结尾，执行文件复制并重命名。

- 如果 `SRC` 是多个文件：

  - `DEST` 必须为目录且存在，否则报错。

- 如果 `SRC` 是目录：

  > 必须带 `-r` 参数，表示递归复制，否则会提示：`cp: 略过目录"/tmp/a"`

  - 如果 `DEST` 不存在：创建指定的目录，并将 `SRC` 下的内容全部复制到 `DEST` 中。
  - 如果 `DEST` 存在：
    - 是文件：报错：`cp: 无法以目录"/tmp/a" 来覆盖非目录"/tmp/b"`
    - 是目录：将 `SRC` 目录复制到 `DEST` 目录下，并作为子目录。



## 常见用法

### 复制单个文件

拷贝一个文件到指定的目录，拷贝后，文件名保持不变。如果 DEST 存在，路径是不是以 / 结尾都一样。

```sh
$ cp /tmp/1.md /tmp/b
```

如果觉得写文件的绝对路径太长，可以先 cd 切换工作目录到源目录，使用相对路径形式：

```sh
$ cd /tmp
cp 1.md /tmp/b
```



### 复制多个文件

```sh
$ cp 1.md 2.md 3.md /tmp/b
```

多个源文件用空格隔开，也可以使用 -t 参数，将多个源文件写在后面：

```sh
$ cp -t /tmp/b 1.md 2.md 3.md
```

也可以使用通配符 * 表示目录下的所有文件：

```sh
$ cp /tmp/a/* /tmp/b
```

上面仅复制 /tmp/a 目录下的文件，如果希望连同子目录也要复制，则使用 -r 参数：

```sh
$ cp -r /tmp/a/* /tmp/b
```

上面的命令是复制整个目录，这种情况可以将 /* 省略：

```sh
$ cp -r /tmp/a /tmp/b
```



### 复制并重命名

重命名时，SRC 和 DEST 必须都要为文件，且 DEST 文件的父路径要存在。

```sh
$ cp /tmp/1.md /tmp/b/x1.md
```



### 拷贝目录

拷贝目录时，必须使用 -r 选型，表示递归，否则报错：SRC 目录被忽略。

```sh
$ cp -r /tmp/a /tmp/b
# 等效于
$ cp -r /tmp/a/* /tmp/b
```

上面将 /tmp/a 目录拷贝到 /tmp/b，这里分两种情况：

-  如果 /tmp/b 不存在：创建 /tmp/b 目录，且将 /tmp/a 目录中的所有内容复制到 /tmp/b 中。
- 如果 /tmp/b 存在：将 /tmp/a 下的所有内容拷贝到 /tmp/b/a 中。

NOTE：复制目录和上面复制文件并重命名的行为类似，只是需要加 -r 参数，且必须要保证 DEST 父路径要存在。



### 显示复制过程都做了什么

加上 -v 或 --verbose 参数：

```sh
cp -v *.md /tmp/b
"1.md" -> "/tmp/b/1.md"
"2.md" -> "/tmp/b/2.md"
"3.md" -> "/tmp/b/3.md"
```



### 复制父路径目录结构

如果希望源文件被复制后，在 `DEST` 中的父路径目录结构保持和 `SRC` 中的相同，比如 存在一个文件 `/tmp/a/src/main/java/1.md`，现在我们希望将 `1.md` 拷贝到 `/tmp/b/src/main/java/` 中。

可以使用 --parents 参数：

```
--parents
              use full source file name under DIRECTORY
```

该参数意思是：使用完全的源文件名及路径，复制后目标文件所处的目录结构和源文件名中指定的路径相同：

```sh
$ cd /tmp/a
$ cp --parents src/main/java/1.md /tmp/b
```

此时，**DEST 目录必须要存在**。想想看，`-r` 参数能实现这种场景需求吗？



### 文件覆盖与不覆盖

第一种情况：只复制新修改过、或者不存在的文件，使用 `-u` 参数：

```sh
# 当源文件比目标文件新，或目标文件不存在时才进行复制，且不会提示是否要覆盖
$ \cp -u /tmp/a/* /tmp/b
```

注意：上面使用 `\cp` 避免运行 `alias` 中定义的 `cp = cp -i`，因为带上`-i` 后就，会在文件存在时提示时是否要覆盖。

第二种情况：不希望覆盖已存在的，仅复制目标目录中不存在的文件：

```sh
$ cp -n /tmp/a/* /tmp/b
```

使用了 `-n` 参数，就意味着 `-i` 参数无效了。



### 以交互方式运行

使用 `-i` 参数表示以交互方式运行，当有文件需要覆盖时会提示：

```sh
$ cp -i /tmp/a/* /tmp/b
cp：是否覆盖"/tmp/b/1.md"？
```

输入 `y` 并回车，表示要覆盖，直接回车表示不覆盖。

默认情况下，我们使用的 `cp` 命令其实就是以交互方式运行，因为它只是一个 `alias`。

```sh
$ alias cp
alias cp='cp -i'
```

如果想执行原始 `cp` 命令，要么去掉 `alias`（一般定义在 `~/.bashrc` 文件中）的定义；要不在命令前加 `\` ：

```sh
$ \cp /tmp/a/* /tmp/b
```



### 创建软/硬链接

有时候我们只想创建一个链接（快捷方式）而不是拷贝它们，除了使用 ln 命令创建硬链接之外，cp 命令也可以做到，只要带上 -l 选项：

```sh
$ cp -l /tmp/a/1.md /tmp/b
# 创建文件的硬链接，并重命名
$ cp -l /tmp/a/1.md /tmp/b/x1.md
```

等效于：

```sh
$ ln /tmp/a/1.md /tmp/b
# 创建文件的硬链接，并重命名
$ ln /tmp/a/1.md /tmp/b/x1.md
```

如果想要创建软链接（符号链接），请使用 -s 参数：

```sh
$ cp -s /tmp/a/1.md /tmp/b
# 创建软连接并重命名
$ cp -s /tmp/a/1.md /tmp/b/x1.md
```



### 对于软连接的复制（TODO）

对于符号链接文件或目录，默认

#### 拷贝符号链接本身不拷贝内容

对于符号链接（软连接），默认情况下会拷贝符号链接对应的文件内容而非符号链接。如果我们只想原样将符号链接拷贝过去，可以使用 -P 参数：

```sh
$ cd /tmp; mkdir a b c
# 先在 /a 目录中创建一个文件
$ touch a/1.md
# 在 b 中创建一个软链接指向 a/1.md
$ cp -s /tmp/a/1.md /tmp/b; ll b
lrwxrwxrwx 1 root root 11 9月  17 15:57 1.md -> /tmp/a/1.md

# 默认情况下，对于软连接，是复制其对应的文件内容
$ cp /tmp/b/1.md /tmp/c; ll c
-rw-r--r-- 1 root root 0 9月  17 16:27 1.md

# 删除掉之前复制到 /tmp/c 的 1.md, 将尝试仅复制软连接本身
$ rm -f /tmp/c/1.md
# 加 -P 参数，将 b/1.md 拷贝到 /tmp/c 中
# 可以看到，符号链接被原样拷贝过去了，而非复制其指向的文件
$ cp -P b/1.md /tmp/c; ll c
lrwxrwxrwx 1 root root 11 9月  17 16:00 1.md -> /tmp/a/1.md
```



#### 拷贝符号链接对应的内容而非符号链接

选型 -L 表示复制内容而非链接自身，与 -P 选项正好相反。其实默认行为对于符号链接也是复制其指向的文件内容，所以 -L 也可以省略。

```sh
$ cp -L /tmp/a/1.md /tmp/b
# 等效于
$ cp /tmp/a/1.md /tmp/b
```





### 复制并备份

默认情况下，发现目标目录中有同名文件时会覆盖，使用 --backup 选型可以先备份目标目录中同名的文件：

```sh
$ \cp --backup=simple -v /tmp/a/*.md /tmp/b
"1.md" -> "/tmp/b/1.md" (备份："/tmp/b/1.md~")
"2.md" -> "/tmp/b/2.md" (备份："/tmp/b/2.md~")
```

> NOTE：加 \ 是为了避免使用 `cp` 命令的别名，因为那个带了 `-i` 选型，始终以交互方式运行，遇到同名文件需覆盖时会提示是否要覆盖。

-`-backup` 参数可以指定不同的备份控制，可以使用 `man cp` 查看使用方法。

- `none, off`：never make backups (even if --backup is given)
- `numbered, t`：make numbered backups
- `existing, nil`：numbered if numbered backups exist, simple otherwise
- `simple, never`：always make simple backups

下面指定 `backup=t` 看下备份文件名有什么不同：

```sh
$ \cp --backup=t -v *.md /tmp/b
"1.md" -> "/tmp/b/1.md" (备份："/tmp/b/1.md.~1~")
"2.md" -> "/tmp/b/2.md" (备份："/tmp/b/2.md.~1~")
$ \cp --backup=t -v *.md /tmp/b
"1.md" -> "/tmp/b/1.md" (备份："/tmp/b/1.md.~2~")
"2.md" -> "/tmp/b/2.md" (备份："/tmp/b/2.md.~2~")
$ \cp --backup=t -v *.md /tmp/b
"1.md" -> "/tmp/b/1.md" (备份："/tmp/b/1.md.~3~")
"2.md" -> "/tmp/b/2.md" (备份："/tmp/b/2.md.~3~")
```

还有个 `-b` 选项，和 `--backup` 类似，只是不能指定参数，使用默认行为（即：`--backup=simple`）

另外，可以使用 `-S, --suffix=SUFFIX` 选型指定备份文件后缀。



### 只拷贝文件属性，不拷贝数据

```sh
$ echo hello > /tmp/a/1.md; 
$ ll /tmp/a/1.md
-rw-r--r-- 1 root root 6 9月  17 16:49 1.md

$ cp --attributes-only /tmp/a/1.md /tmp/b; 
$ ll /tmp/b/1.md
-rw-r--r-- 1 root root 0 9月  17 16:49 /tmp/b/1.md

$ cat /tmp/b/1.md
# 没有显示期望的内容：hello
```



### 强制拷贝

如果已存在目标文件且不能打开，加 -f 选项会先删除它然后重试，这个选项会忽略 -n 选项（-n 表示不希望覆盖已存在的）

```sh
$ cp -f /tmp/a/1.md /tmp/b/
```



### 在拷贝之前先删除目标

```sh
$ \cp --remove-destination -v /tmp/a/1.md /tmp/b
已删除"/tmp/b/1.md"
"/tmp/a/1.md" -> "/tmp/b/1.md"
```



### 复制时保留目录结构和文件属性

有时候，我们需要复制时保留文件的结构和属性，这时我们可以使用 `-a` 选型：

`-a` 相当于 `-dR --preserve=all`

`-d` 相当于 `--no-dereference --preserve=links`

`-R` 表示递归复制

合并同类项，使用了 `-a` 相当于指定 `--no-dereference -R --preserve=all`，意思是递归复制所有子目录（`-R`），且保留所有属性（`--preserve=all`）,且如果碰到有符号链接时近复制符号本身而非内容（`--no-dereference`）。

```sh
$ cp -a /tmp/a /backup
```

上面的命令执行后，`/tmp/a` 目录及其下面的内容被备份到 `/backup` 目录下，所有文件属性保持相同，如果有符号链接也会保持链接本身。



## 容易迷惑的几种情况

1. 目标目录不存在的情况下，执行如下命令：

   ```sh
   $ cp /tmp/a/1.md /tmp/b
   ```

   期望的结果：将 1.md 复制到 /tmp/b 下，实际结果：1.md 被复制到 /tmp 目录下，且被重命名为 b。

2. 目标目录不存在的情况下，执行如下命令两次会产生不一样的结果：

   ```sh
   $ cp -r /tmp/a /tmp/b
   ```

   期望的结果：将  `/tmp/a` 下的所有文件（包含子目录）复制到 `/tmp/b` 目录中，如果 `/tmp/b` 目录不存在，则自动创建，实际的结果：符合预期。但是，**第二次执行会得到不一样的结果**，第二次执行时，由于 /tmp/b 已经存在了，所以 `/tmp/a` 目录中的内容被复制到 `/tmp/b/a` 中了。
   
   为避免上述迷惑，可遵循下面的几点建议：
   
   1. 复制前，每次都先确保目标文件夹存在。
   2. 复制前，每次都先确保目标文件夹不存在。
   



## TODO

-H、-L

-L 对于硬链接的处理

对于



# 2、rsync 和 scp 命令(TODO)

# 3、docker cp 命令 （TODO）

docker cp 命令用以在本地文件系统和容器之间拷贝文件或目录。



## 用法及命令选项

```
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

命令选项：

- --archive , -a：归档模式，拷贝所有的 uid/gid 信息。
- --follow-link , -L：拷贝符号链接对应的内容，而非符号链接本身

和 linux cp 命令带有很多选项不同，它只有两个选项：



## 命令特点描述

- SRC_PATH 或 DEST_PATH 可以指定 -，表示从标准输入（STDIN）/输出设备（STDOUT）以流的方式读取或写入 tar 文件。
- 不管容器有没有启动，拷贝命令都会生效。
- 如果拷贝的内容有重复，会直接覆盖，不会询问。
- 默认认为容器路径是相对于根目录（/）的，因此容器路径可以省略前置的 /，也就是说：nginx:/tmp 和 nginx:tmp 是等效的。
- 本地文件系统路径可以是绝对路径也可以是相对路径（相对于当前工作目录）。

  

## 和 cp 命令的差异

1. docker cp 类似于 linux cp -a 命令，递归复制目录下的文件结构及保留文件属性。
2. 







# 4、参考

1. https://blog.csdn.net/jb19900111/article/details/21460423?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf
2. https://docs.docker.com/engine/reference/commandline/cp/
3. https://unix.stackexchange.com/questions/94714/cp-l-vs-cp-h