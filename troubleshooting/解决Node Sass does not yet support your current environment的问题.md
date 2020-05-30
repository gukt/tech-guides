### 问题描述：

```
Error: Node Sass does not yet support your current environment: OS X 64-bit with Unsupported runtime (72) 
```



### 解决方案：

错误是说不支持当前环境，卸载掉重新安装即可。

```sh
# 卸载
npm uninstall --save node-sass
# 清除缓存
npm cache clean -f
# 安装
npm install --save node-sass
```

