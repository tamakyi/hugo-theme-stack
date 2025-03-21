---
title: "添加acfun直播的颜文字（仅针对handsome主题）"
categories: [ "记录" ]
tags: [  ]
draft: false
slug: "aclive-emotion"
date: "2022-07-07 00:59:00"
---

很简单，直接把对应文件替换掉就好。
后续会把ac娘也接入。
[hide]https://blog.tama.guru/usr/themes/handsome/usr/OwO.json[/hide]

PS:AC娘图标包也已经实装
只需要把[hide]https://cdn.aixifan.com/dotnet/20130418/project/emot/package2.0/acfun_emoji.zip[/hide]下载到服务器解压并将压缩包下`图片集`文件夹移动到`usr/themes/handsome/assets/img/emotion`改名为`acfun`再用上面的文件替换即可。

如果觉得显示的表情过大了，可以在主题设置中的自定义css添加：

    /*滑稽小表情自定义大小*/
    .emotion-funny {
        width: 30px;
    }
    .comment-content-true img.emotion-funny {
        max-width: 100%!important;
    }