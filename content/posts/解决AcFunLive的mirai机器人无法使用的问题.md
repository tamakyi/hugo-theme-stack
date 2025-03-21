---
title: "解决AcFunLive的mirai机器人无法使用的问题"
categories: [ "记录" ]
tags: [ "miraiqq","acfunlive","gocqhttp" ]
draft: false
slug: "acfunlive-mirai"
date: "2023-05-05 23:35:47"
---

*注意，mirai无法启动仅针对在windows下使用时出现，linux并无此问题。


<!--more-->

使用最新的acfunlive源码编译windows-gui并使用时发现，执行
```acfunlive.exe -mirai```
会直接开始程序的运行，跳过mirai登录验证场景并报错：
```error: read /dev/stdin: The handle is invalid```
这个问题苦恼了我很久，之前都时再开个linux虚拟机单独跑录播机器人，但是一想到之前也碰到过mirai机器人无法登录的问题，最终解决方式是通过gocqhttp生成device和session文件替换到程序目录中以骗过程序你已经登陆过了。
以下是操作流程：

 1. 下载并配置go-cqhttp
到官网下载当前环境配置版本的go-cqhttp，此处我是windows，直接下载[windows_x64][1]版本。下载后双击运行exe，提示无法直接运行，之后生成一个bat文件，双击`go-cqhttp.bat`文件运行，然后输入1回车，提示`默认配置文件已生成，请修改 config.yml 后重新启动!`后关闭窗口。
![2023-05-05T15:19:44.png][2]
修改`config.yml`，主要是修改uin这一项，其他不用管（因为之后使用的是watch协议登录，所以不需要填密码），修改后保存文件。
![2023-05-05T15:21:51.png][3]
再次双击`go-cqhttp.bat`文件运行，在提示`Bot将在5秒后登录并开始信息处理, 按 Ctrl+C 取消.`后按Ctrl+C中断程序运行。
![2023-05-05T15:24:27.png][4]
然后目录下会生成一个`device.json`文件，打开并将`"protocol":6`的值修改成`2`，保存。然后再次双击`go-cqhttp.bat`文件运行，一段时间后他会提示你扫码登录，使用登录了同一个账号的手机扫码会提示是一个手表设备，确认登录。等出现如下内容时可以关闭程序。并看到目录下生成了`sesssion.token`这个文件。
![2023-05-05T15:29:31.png][5]
 2. 修改acfunlive配置
把gocqhttp目录下的`sesssion.token`和`device.json`复制到acfunlive目录（保险起见可以全部复制过去），然后将其重命名为`qqsesssion.token`和`qqdevice.json`，此时目录结构如下所示。
![2023-05-05T15:32:12.png][6]
此时在powershell下执行`.\acfunlive.exe -mirai`或者正常启动到`http://localhost:51880/startmirai`再打开`http://localhost:51880/log`可以看到已经登录成功了。
![2023-05-05T15:34:40.png][7]
如果提示要扫码，那么会在acfunlive的目录下有一个`qqqrcode.png`文件，打开用手机扫码登录即可。


  [1]: https://github.com/Mrs4s/go-cqhttp/releases/download/v1.0.1/go-cqhttp_windows_amd64.exe
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/05/64551e91cd25b.png
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/05/64551f1085317.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/05/64551fac3c9af.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/05/645520dc60a55.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/05/6455217dd9f5c.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/05/64552211c7de8.png