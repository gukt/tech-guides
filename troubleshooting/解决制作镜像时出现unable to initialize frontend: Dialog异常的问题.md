## 问题描述

制作镜像时出现异常：unable to initialize frontend: Dialog

```sh
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (This frontend requires a controlling tty.)
debconf: falling back to frontend: Teletype
```

原因是因为使用apt-get安装依赖时，可能会有对话框，制作镜像时如果没有对对话框响应会导致失败。



## 解决方案

在出错指令之前加上：

```sh
ENV DEBIAN_FRONTEND noninteractive
```

