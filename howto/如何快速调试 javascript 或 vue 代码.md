这是我目前最常用的，快速对 JavaScript 或 Vue 进行原型开发、调试的方式。



首先，要安装一个全局扩展：

```sh
$ npm install -g @vue/cli-service-global
```

然后，就可以开心地使用 `vue serve` 命令对 `.vue` 文件或 `.js` 文件进行调试了：

```sh
$ vue serve foo.js
$ vue serve foo.vue
```

等待编译完成后，会提示你浏览器访问的地址：

```sh
 DONE  Compiled successfully in 155ms                                         上午4:13:32


  App running at:
  - Local:   http://localhost:8082/
  - Network: http://192.168.1.3:8082/
```

使用 `-o` 参数，编译成功后，可以自动打开浏览器：

```sh
$ vue serve foo.js -o
```

官方参考文档在 [这里](https://cli.vuejs.org/zh/guide/prototyping.html)。

如果你安装了 [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd) 浏览器插件，开发和调试 Vue 应用会更加方便。

更加人性化的是：修改了代码，保存后，浏览器会自动刷新。



（完）