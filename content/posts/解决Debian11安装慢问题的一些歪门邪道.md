---
title: "解决Debian11安装慢问题的一些歪门邪道"
categories: [ "记录" ]
tags: [ "debian","linux" ]
draft: false
slug: "debian_install_slow"
date: "2023-03-08 16:02:24"
---

前几天安装Debian的时候，在`Select and install software`过程总是报错后终止安装进程，强制进入下一步安装引导会提示没有安装grub引导器可能会导致无法启动，查了一些资料后都在说是`debian-security`这个源在国外，直接连接速度极慢（体现为这一步下载4个文件需要几个小时，而且极大可能会突然连接不上然后安装失败）。
那么就有说在这一步之前选择`不使用网络源`并且按`Ctrl+Alt+F2`切换到其他tty：
修改`/target/etc/apt/sources.list`
```
#修改debian-security为国内源或者直接屏蔽掉这一行
#deb http://security.debian.org/debian-security bullseye-security main
```
但是实际就算是把源换成国内的或是屏蔽掉，在点击下一步之后还是会把`security.debian.org`这个源还原回来，然后继续卡着...
那么就有两种办法，
###一、全局代理###
这种方法我还没试过，不过觉得是可行的，需要在路由器或者上级网络配置好全局国外代理。使用手机USB网络或者热点可能有些问题，因为在这一步WIFI和USB网卡驱动还没能安装。
###二、拔网线###
在进入`是否选择网络源`这个界面的时候把网线拔了再点下一步，这样就默认只使用本地介质上的资源进行更新。最好不要全程断网（进系统之后你需要自己配置网卡设置）或是在这之前断网（尝试在DHCP配置完成之后、是否选择网络源之前拔掉网线，会有可能导致安装进程卡住。），等待安装完成后再把网线接回去。