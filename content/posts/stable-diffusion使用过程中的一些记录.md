---
title: "stable-diffusion使用过程中的一些记录"
categories: [ "记录" ]
tags: [ "stable-diffusion","novelai" ]
draft: false
slug: "stable-diffusion-webui"
date: "2022-10-19 23:44:38"
---

原文已丢失，以下部分为重写。
正文部分忘得差不多了，此处就记录一下使用过程中的问题吧。


<!--more-->

[scode type="lblue"]No.1 我想在本地搭建服务并分享出去[/scode]
修改目录下的`webui.py`文件，将101行`app,local_url,share_url = demo.launch(`的代码块修改为：
```
        app,local_url,share_url = demo.launch(
            share=True,
            server_name="你的本地IP，比如192.168.10.121" if cmd_opts.listen else None,
            server_port=cmd_opts.port,
            debug=cmd_opts.gradio_debug,
            auth=[tuple(cred.split(':')) for cred in cmd_opts.gradio_auth.strip('"').split(',')] if cmd_opts.gradio_auth else None,
            inbrowser=cmd_opts.autolaunch,
            prevent_thread_lock=True
        )
```
修改完成后保存运行`webui-user.bat`即可。
需要注意的是开启分享需要全程连接谷歌，因为需要调用到谷歌的gradio服务。
[scode type="lblue"]No.2 我想在本地搭建服务但是我的运存有点小[/scode]
按照官方的说法，只需要修改`webui-user.bat`文件，将`set COMMANDLINE_ARGS=`添加参数`--medvram`即可，即显示为：
`set COMMANDLINE_ARGS=--medvram`
修改完成后保存运行`webui-user.bat`即可。
[scode type="lblue"]No.3 我生成的结果好丑！[/scode]
我猜你是想画二次元涩图。其一：程序的wiki上留下的模型比较...正常？所以首先你需要替换掉模型。目前因为一些原因novelai的ckpt模型已泄露，可以将下载到的模型重命名为`model.ckpt`并替换到`models\Stable-diffusion`目录再重新启动程序即可。
其二：你需要到网页端把NSFW过滤给关掉了。
其三：你可能需要添加一些低质量关键词过滤，以下为参考：
`lowres, bad anatomy, bad hands, text, error, missing fingers, extra digit, fewer digits, cropped, worst quality, low quality, normal quality, jpeg artifacts, signature, watermark, username, blurry, bad feet`