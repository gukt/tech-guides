> 以下内容为[这篇官方文档](http://nginx.org/en/docs/http/server_names.html)的翻译，略去一些细节以及添加了一些自己的理解。



# 1、概述

服务器名称使用server_name指令定义，用来决定哪个server块执行请求，它可以定义为三种形式：

1. 精确的名称：`example.org`，`www.example.org `
2. 通配符名称：`*.example.org`，`example.*`
3. 正则表达式名称：`~^(?<user>.+)\.example\.net$`

如果在搜索server块时发现多个名称都可以匹配，则依照下面的优先级顺序：

1. 精确的名称
2. 以*开头的通配符名称
3. 以*结尾的通配符名称
4. 第一个匹配到的正则表达式名称（以在配置文件中出现的顺序）



# 2、通配符名称

通配符名称中包含`*`，它**只能出现在名称的开头或者结尾**，且必须以.为边界，这些通配符名称都可以用正则来表示。星号可以匹配多个名称部分，比如`*.example.org`可以匹配`www.example.org`，还可以匹配`www.sub.example.org`。

一个特别的**通配符名称**：`.example.org`，它相当于`example.org` + `*.example.org`。注意：不能因为它可以匹配example.org就把这种形式理解为它是`精确的名称`，**从而错误的认为它会在优先于通配符名称之前匹配**。

以下名称是错误的：

1. `www.*.example.org`（没有以`*`开头或结尾）
2. `w*.example.org`（没有以`*`开头或结尾）
3. `*1.example.org`（虽然以`*`开头，但是没有以`.`为边界）



# 3、正则表达式名称

nginx使用的正则表达式和PCRE兼容，以波浪号`~`开头，否则会把他当成精确名称对待。

> server_name ~^www\d+\.example\.net$;

一般情况下，**建议在正则中使用`^`和`$`**，它们表示开头和结尾，**但这不是必须的**。**点号`.`必须要用转移符`\`**。

**如果正则表达式中包含`{`和`}`，则需要用引号括起来**（单引号或双引号均可）

可以使用命名的正则表达式**捕获**，**这个名称可以作为变量供后续使用**。

```nginx
server {
	server_name 	~^(www\.)?(?<domain>.+)$
	location / {
		root	/sites/$domain;
	}
}
```

也开始使用`$1`,`$2`这种数字形式来获取捕获（capture）。但是**不建议使用这种形式，因为它们很容易被覆盖**。



# 4、混合的名称

server_name指令可以指定多个名称，用空格隔开，`""`表示匹配`header`中没有`Host`为空的请求。

**名称也可以是IP地址**。以支持使用直接的IP地址访问。

```nginx
server {
    listen       80;
    server_name  example.org
                 www.example.org
                 ""
                 192.168.1.1
                 ;
    ...
}
```

名称还可以是`_`，**表示捕获所有无效的名称**，这些无效的名称比如`--`，`!@#`

```nginx
server {
    listen       80  default_server;
    server_name  _;
    return       444;
}
```

在0.6.25版本之前，还有个特殊的名称`*`，不过现在已经废弃了，使用 [server_name_in_redirect](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name_in_redirect) 代替。

请注意：server_name指令中**没有办法设定一个名称可以捕获所有名称**或default server。**这是listen指令的所有权**，而不是server_name指令。

```nginx
server {
    listen       80;
    listen       8080  default_server;
    server_name  example.net;
    ...
}

server {
    listen       80  default_server;
    listen       8080;
    server_name  example.org;
    ...
}
```

以上的设置表明：

1. 第一个server块匹配所有来自8080端口的请求（example.org除外，因为会被第二个严格匹配到）
2. 第二个server块匹配所有来自80端口的请求（example.net除外，因为会被第一个严格配到）



# 5、最佳实践

对于1：精确的名称，2：以`*`开头的通配符名称，3：以`*`结尾的通配符名称，它们会分别放入三个不同的hash table中，顺序是从1，2，3的hash table中依次搜索。

第一个哈希表的搜索最快，只要做字符串比较即可，对于通配符的检索会慢一点，因为需要匹配域名组成部分。**再次提醒一下：特殊的通配符`.example.org`是保存在通配符哈希表中的**。

正则表达式的匹配是最慢的。

所以，基于以上描述，最好是优先考虑使用`精确名称匹配`，对于经常要访问的名称，最好明白的列出来：

```nginx
server {
    listen       80;
    server_name  example.org  www.example.org  *.example.org;
    ...
}
```

而不是使用简化的形式：

```nginx
server {
    listen       80;
    server_name  .example.org;
    ...
}
```

说明：前面讲过，`.example.org` =  `example.org` + `*.example.org`

第一种方式最好，因为`example.org`和`www.examle.org`是最常用的，所以明确的列出来检索是最快的，一旦检索到就立即中断后续的检索。

如果定义的名称非常长，有必要考虑调整http块中的 [server_names_hash_max_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_max_size) 和 [server_names_hash_bucket_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_bucket_size) 指令，值是按32的幂递增的。

如果名称太长，超出了server_names_hash_bucket_size设定的大小，则会抛出以下异常：

```shell
could not build the server_names_hash,
you should increase server_names_hash_bucket_size: 32
```

解决方式是将32跳整为按2次方递增即可，比如设置为64。

如果server_name定义太多，超出了server_names_hash_max_size定义的大小，则会抛出如下异常：

```shell
could not build the server_names_hash,
you should increase either server_names_hash_max_size: 512
or server_names_hash_bucket_size: 32
```



（完）