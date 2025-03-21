---
title: "使用S3 Browser管理Cloudflare R2存储桶"
categories: [ "记录" ]
tags: [ "cloudflare","R2","S3" ]
draft: false
slug: "s3browser_r2_manager"
date: "2024-01-13 22:53:02"
---

本来我是要把导航页所有的文件搬到Github上并**试图**通过jsdelivr加速访问，但是我发现国内移动网络根本无法直连jsdelivr...在多次反馈网页无法打开之后，最后还是退一步选择迁移到R2上了。但是R2的网页端管理真的是做得一团糟，上传经常半路失败不说，一次还只能上传100个文件，更恶心的是文件夹无法删除，对于我来说是体验真的不好了。


<!--more-->

既然兼容AWS S3，那就用S3的方法去管理他。
需要用到[S3Browser][1]这个软件，只有英文，但是不妨碍使用。
安装完成之后选择`Add new account`。
`Display Name`任意填写即可，`Account type`选择`S3 Compatible Storage`，即兼容S3存储。`REST Endpotin`对应的是`S3 API`的值，但是不能整个填写进去。比如网页端显示的`S3 API`为：
```
https://xxxxxxxxxxxxxxxxxxx.r2.cloudflarestorage.com/tamabox
```
那这里要填写的是
```
xxxxxxxxxxxxxxxxxxx.r2.cloudflarestorage.com
```
`AK`和`SK`是创建桶的时候提供的，如果忘记了就没救了，重新弄个桶吧。
`Encrypt Access Keys with a password`和`Use secure transfer(SSL/TSL)`选项保留默认即可。
![add account info 1][2]
配置完之后要点开左下角的`advanced settings`。
`Signature version`选择`Signature V4`.
`Addressing model`选择`Virtual hosted style`.
其他选项保留初始值不用动，点close再save changes`即可。
![add account info 2][3]
这时候应该是能连接进R2内部了，就当作一个文件管理器操作就行了。
![setting complete][4]
<br/>
如果只用来做图床的话可以用`Pic-Go`这个软件，操作方法也类似。


  [1]: https://s3browser.com/download.aspx
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/13/65a2a0bdc8b8c.png
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/13/65a2a2b462a5b.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/13/65a2a37d563a3.png