---
title: "安装最新版nodejs的正确方法"
categories: [ "记录" ]
tags: [ "nodejs" ]
draft: false
slug: "iwannanewnodejs"
date: "2022-12-31 21:13:00"
---

 ::acfun:02:: 安装这个nodejs真的要命！首先是官方库里面的版本过老，如果通过添加源的方式更新一方面是更新速度极慢，另一方面则是可能会碰到“由于没有公钥，无法验证下列签名”这个怪问题，查了好多文章都无法解决。


<!--more-->

替换树莓派默认的node.js和npm
一、删除默认安装的软件
卸载nodejs
`sudo apt-get remove nodejs`
清除软件包以及相关配置
`sudo apt-get purge nodejs`
二、查看内核版本：
`Linux tamapi 5.15.76-v8+ #1597 SMP PREEMPT Fri Nov 4 12:16:41 GMT 2022 aarch64 GNU/Linux`
aarch64 那我就选arm64版本的
三、下载最新版的 nodejs：此处是node19
`https://nodejs.org/dist/latest-v19.x/node-v19.3.0-linux-arm64.tar.gz`
四、解压压缩包
`tar -zxvf node-v19.3.0-linux-arm64.tar.gz`
五、删除系统上的旧目录（如果存在）
`sudo rm /usr/bin/node`
`sudo rm /usr/bin/npm`
六、将文件复制到指定位置
`sudo mv ./node-v19.3.0-linux-arm64 /usr/local/node`
七、node 和 npm 创建软连接
`sudo ln -s /usr/local/node/bin/node /usr/bin/node`
`sudo ln -s /usr/local/node/bin/npm /usr/bin/npm`
八、检查软件安装是否成功
`node -v`
v19.3.0
`npm -v`
9.2.0