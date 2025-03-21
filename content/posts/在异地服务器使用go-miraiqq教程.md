---
title: "在异地服务器使用go-miraiqq教程"
categories: [ "记录" ]
tags: [ "go-cqhttp","miraiqq" ]
draft: false
slug: "miraiqq-remote"
date: "2022-07-06 09:11:45"
---

在异地直接扫码登录miraiqq会有“异常，建议在同一WIFI环境下使用”的提示，如果服务器在本地还好，但是买的服务器在外地就很难这样去处理了。
解决的方式也很简单，因为go-cqhttp是通过检查运行目录下的 `device.json`和 `session.token`来验证数据的，因此只要在本地环境搭建好go-cqhttp环境并登陆，然后用本地生成的两个文件传输到远程服务器下替换掉原有文件就好了。

---

具体步骤：

1. 本地搭建go-cqhttp（本地的服务器类型可以与异地不一致，因为最终生成的 `device.json`和 `session.token`内容是一样的）
   到 `https://github.com/Mrs4s/go-cqhttp/releases`下载本地系统对应版本的文件，例如使用windows的可以下载 `https://github.com/Mrs4s/go-cqhttp/releases/download/v1.0.0-rc3/go-cqhttp_windows_amd64.exe`，下载到本地双击运行；使用debian系的可以 `wget https://github.com/Mrs4s/go-cqhttp/releases/download/v1.0.0-rc3/go-cqhttp_1.0.0-rc3_linux_amd64.deb` 然后 `dpkg -i go-cqhttp_1.0.0-rc3_linux_amd64.deb`，即可使用go-cqhttp命令在当前目录下运行。
2. 使用windows版本会提示请勿通过双击运行本程序，确认即可，后续会生成 `go-cqhttp.bat`，双击运行 `go-cqhttp.bat`按要求选择通信方式，一般选择 `0:HTTP通信`即可，待提示 `默认配置文件已生成，请修改 config.yml 后重新启动!`后可关闭窗口。
3. 之后修改 `config.yml`，此处只需要填报QQ账号、密码 其他不要动。
4. 修改好之后再运行 `go-cqhttp.bat`一般会提示两个步骤：一是要滑动验证，直接复制生成的网址到浏览器并按要求处理即可；二是选择使用手机接收验证码。处理完两个步骤后提示正常运行即可关闭窗口，然后把目录下的 `device.json`和 `session.token`传输到服务器的miraiqq目录再运行对应程序即可（注意服务器的config文件中QQ账号、密码要和本地一致）。
   PS： linux大同小异。
