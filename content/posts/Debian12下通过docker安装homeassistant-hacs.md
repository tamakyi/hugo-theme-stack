---
title: "Debian12下通过docker安装homeassistant-hacs"
categories: [ "记录" ]
tags: [ "homeassistant","hass","米家","docker","supervised" ]
draft: false
slug: "homeassistant-hacs-debian12"
date: "2023-08-25 11:58:39"
---

HACS即`Home Assistant Community Store`，整合了很多homeassistant的第三方集成，比如说米家的相关设备，如果使用官方自带的集成，一般只能识别出几个传感器，而且无法控制，如果想要把米家的设备完全接入就需要安装HACS下面的Xiaomi Miot Auto这个集成，安装之后效果如图所示，基本上能实现HASS管理全部米家设备的想法。
![安装集成之后的页面][1]
安装HACS难的地方在于需要homeassistant supervised版本，要么在物理机上安装hassos，要么弄个虚拟机给hass，还有一种方法是在debian系的系统上通过docker实现，因为前面两种方法影响太大，我选择第三个。
需要注意的是最好使用原版debian系统，我现在用的是debian 12.1，正好适合。

----------
####首先肯定是先安装docker
```
apt install curl vim wget gnupg dpkg apt-transport-https lsb-release ca-certificates #安装必要组件
curl -sS https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list #添加源和证书
```
####安装OS-AGENT和supervised
```
wget https://github.com/home-assistant/os-agent/releases/download/1.5.1/os-agent_1.5.1_linux_x86_64.deb
dpkg -i os-agent_1.5.1_linux_x86_64.deb

wget https://github.com/home-assistant/supervised-installer/releases/download/1.5.0/homeassistant-supervised.deb
dpkg -i homeassistant-supervised.deb
```
安装supervised包时可能会报错，需要安装以下依赖包：
```
apt install libmicrohttpd12 systemd-journal-remote systemd-resolved libnss-resolve jq
```
supervised包安装好后要重启一次机器，不然有可能会遇到解析`checkonline.home-assistant.io`错误的情况。
重启之后在终端里执行
```
docker ps
```
可以看到出现了很多进程。
```
~ sudo docker ps
CONTAINER ID   IMAGE                                                      COMMAND               CREATED       STATUS          PORTS                                   NAMES
80d950683f05   ghcr.io/home-assistant/amd64-hassio-multicast:2023.06.2    "/init"               3 hours ago   Up 3 hours                                              hassio_multicast
e2c45b161283   ghcr.io/home-assistant/amd64-hassio-audio:2023.06.0        "/init"               3 hours ago   Up 3 hours                                              hassio_audio
61df701e5ca7   ghcr.io/home-assistant/amd64-hassio-dns:2023.06.2          "/init"               3 hours ago   Up 3 hours                                              hassio_dns
a6d8e3385086   ghcr.io/home-assistant/amd64-hassio-cli:2023.06.0          "/init"               3 hours ago   Up 3 hours                                              hassio_cli
9c80287b1474   ghcr.io/home-assistant/qemux86-64-homeassistant:2023.8.4   "/init"               3 hours ago   Up 36 minutes                                           homeassistant
7f3cb9ef899e   ghcr.io/home-assistant/amd64-hassio-observer:2023.06.0     "/usr/bin/observer"   3 hours ago   Up 3 hours      0.0.0.0:4357->80/tcp, :::4357->80/tcp   hassio_observer
1e946ecbdc4d   ghcr.io/home-assistant/amd64-hassio-supervisor:latest      "/init"               3 hours ago   Up 3 hours                                              hassio_supervisor
```
这个过程可能需要几分钟，如果可行的话建议还要挂上梯子。几分钟后浏览器访问`http://IP:8123`可以到达hass界面，如果访问不了的建议检查防火墙是否放行8123端口。
####安装HACS并配置Xiaomi Miot Auto
安装HACS只需要在终端里执行
```
wget -O - https://get.hacs.xyz | bash -
```
会自动安装到hass的第三方目录下，安装完成后重启HASS。
然后在HACS下的集成选择浏览并下载存储库，在其中找到Xiaomi Miot Auto，选择安装。
安装完成之后在配置-设备与服务-添加集成-选择Xiaomi Miot Auto，然后输入小米账号ID 密码 服务器选择CN，保存之后所有设备就都出来。 



  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/08/25/64e820b842fb5.png