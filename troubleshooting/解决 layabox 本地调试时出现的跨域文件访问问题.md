默认情况下，浏览器是不允许跨域访问本地文件的。

```sh
Access to XMLHttpRequest at 'file:///Users/ktgu/workspace/projects/HelloLayaBox/bin/test/TestScene.json' from origin 'null' has been blocked by CORS policy: Cross origin requests are only supported for protocol schemes: http, data, chrome, chrome-extension, chrome-untrusted, https.
laya.core.js:18463 file:///Users/ktgu/workspace/projects/HelloLayaBox/bin/test/TestScene.json Request failed Status:0 text:
```

解决方案：

以允许访问本地文件方式启动浏览器。

Mac:

```sh
open -n /Applications/Google\ Chrome.app/ --args --allow-file-access-from-files
```

Windows:

在 chrome.exe 上添加运行参数 `--allow-file-access-from-files`

（完）

