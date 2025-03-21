---
title: "在docker里运行火狐浏览器"
categories: [ "记录" ]
tags: [ "docker","firefox" ]
draft: false
slug: "firefox-on-docker"
date: "2024-04-26 15:53:00"
---

有时候我会需要在远程计算机上使用浏览器进行一些操作，但是存在一些问题。一是远程计算机本身空间容量/配置较低，预装的是core系统，因为使用个浏览器去安装整个桌面环境有点浪费了，也不利于系统稳定；二是软件可能会破坏原有的依赖，轻则软件报错，重则无法启动，这个就很难绷了。
好在docker能实现把远程的浏览器套到本地浏览器运行，看样子是一个webvnc，但是只能打开浏览器。项目是`https://github.com/jlesage/firefox`，方法也很简单，直接执行：

    docker run -d \
        --name=firefox \
        -p 5800:5800 \
        -v /home/tamakyi/firefox/config:/config:rw \
        jlesage/firefox

然后到ip:5800端口就能打开远端的火狐浏览器了。但是这样仅能保证运行，会发现中文字体全部都变成方块了，因此需要调整成：

    docker run -d \
        --name=firefox \
        -p 5800:5800 \
        -v /home/tamakyi/firefox/config:/config:rw \
        -e TZ=Asia/Hong_Kong \
        -e KEEP_APP_RUNNING=1 \
        -e ENABLE_CJK_FONT=1 \
        jlesage/firefox

其中`/home/tamakyi/firefox/config`为宿主机的配置文件目录，需要自行修改，`KEEP_APP_RUNNING=1`标明关掉本地浏览器页面保持远端浏览器运行，`ENABLE_CJK_FONT=1`标明开启中文字体支持。
到这里其实已经能够正常运行了，完整的配置可以参考:

    docker run -d \
        --name=firefox \
        -p 5800:5800 \
        -v /home/tamakyi/firefox/config:/config:rw \
        -e TZ=Asia/Hong_Kong \
        -e KEEP_APP_RUNNING=1 \
        -e ENABLE_CJK_FONT=1 \
        -e DISPLAY_WIDTH=1920 \
        -e DISPLAY_HEIGHT=1080 \
        -e SECURE_CONNECTION=1 \
        --shm-size 2g \
        jlesage/firefox

其他可选性包括：
`DISPLAY_WIDTH`和`DISPLAY_HEIGHT`：生成浏览器的分辨率，如果配置不高的机器建议不要太高。
`SECURE_CONNECTION`：开启https
`shm-size`：配置容器内存使用上限。
执行之后并不会像项目下的源码下能直接访问，会报错，原因可以通过查询docker的运行日志找到：

    [init ] container is starting...
    [cont-env ] loading container environment variables...
    ...
    [cont-init ] 15-cjk-font.sh: installing CJK font...
    [cont-init ] 15-cjk-font.sh: fetch https://dl-cdn.alpinelinux.org/alpine/edge/community/aarch64/APKINDEX.tar.gz
    [cont-init ] 15-cjk-font.sh: fetch https://dl-cdn.alpinelinux.org/alpine/v3.19/main/aarch64/APKINDEX.tar.gz
    [cont-init ] 15-cjk-font.sh: fetch https://dl-cdn.alpinelinux.org/alpine/v3.19/community/aarch64/APKINDEX.tar.gz
    [cont-init ] 15-cjk-font.sh: (1/1) Installing font-wqy-zenhei (0.9.45-r3)
    ...
    ╭――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――╮
    │ │
    │ Application: Firefox │
    │ Application Version: 124.0.1-r0 │
    │ Docker Image Version: 24.04.1 │
    │ Docker Image Platform: linux/arm64 │
    │ │
    ╰――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――╯
    ...
    [app ] Mozilla Firefox 124.0.1
    [supervisor ] all services started.

一般会在`15-cjk-font.sh`这个步骤停留一段时间，需要下载挺多的数据，具体与网络有关。等到提示`all services started`此时才能正常访问。

**注意**：如果你部署在公网服务器上，此时是可以直接通过ip+端口的方式访问的，及其不安全，且默认iptables防火墙无法配置该端口的访问权限...（网上说的方法都试过了，没用）。因此，我建议再套一层firewall-cmd：

    apt install firewalld
    
安装完记得配置常用端口的开启：

    firewall-cmd --zone=public --add-port=YOUR_PORT/tcp --permanent
    firewall-cmd --reload

这样虽然麻烦了点，但是安全性显著提高了。但是有一个问题，就是docker容器**无法联网**了，估计是什么地方冲突，后面再检查。
最后还是**即开即用，用完就关**。

