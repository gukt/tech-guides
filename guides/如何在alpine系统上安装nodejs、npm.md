```sh
#由于jenkins里的node插件使用有问题，只能到容器中直接安装nodejs,npm
apk add --no-cache nodejs npm

#更改为淘宝源，否则chromium下载不下来
npm set registry https://registry.npm.taobao.org/
```

