---
title: "搭了个基于NekoBox的提问箱！"
categories: [ "记录" ]
tags: [ "nekobox","tamabox","提问箱","匿名提问箱" ]
draft: false
slug: "tamabox"
date: "2023-06-03 14:36:00"
---

之前接触过NekoBox这个匿名提问箱，非常小巧，界面简洁不花哨，非常有好感。但是因为一些不方便说明的原因，作者把原站关闭了（**更正，作者已经重新开放原站了**），实在可惜。好在他有把源码放到github（引路：https://github.com/NekoWheel/NekoBox)，虽然基本上相当于没有教程，而且使用过程中有好多报错。经过搭建之后半天摸索，现在功能基本上都正常了。
在此基础上，我引入并魔改了几个js实现了页面二维码以及自定义前景色背景色等小功能，另一方面则是尽量把可以部署的资源都部署到OSS上以实现更快速的访问。![一些修改][1]![二维码自定义颜色][2]
底部加上了没什么用但是就想加上去的信息统计，至少看到数字有变化能盼我感受到:啊啊，原来还有人在用啊。。。

狼的提问箱：[狼的提问箱][3] 欢迎访问和体验！

界面很简洁，很干净。

正如作者所说的“一个究极简洁的匿名问答箱。没有多余的社交元素，只是一问一答。不提醒，不打扰。你不需要下载 App，不需要绑定社交账号，甚至连邮箱都不需要是真的。”
![2023-06-03T06:33:59.png][4]
提问也不需要注册，如果是需要收到提醒就填个接收邮箱就行了：
![2023-06-03T06:34:24.png][5]
再最近一次更新中也实现了图片评论回答功能，一般提问箱的功能基本上能够实现了（用的R2作为图床，别问为什么）:![图片问答][6]
可以自定义提问箱的背景和个人头像，怕被骚扰？也可惜设置仅允许注册用户向我提问，也可以把所有关于你的信息导出，如果想销号也是直接停用：
![2023-06-03T06:34:41.png][7]
如果你正好需要这样一个提问箱，不妨试试，有需要的话也可以自己搭建，最后，欢迎大家向我提问 ::acfun:1001:: 
[小狼的提问箱 - 狼的提问箱][8]


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/27/65b4051a4908d.jpg
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/27/65b4063f2a0ce.jpg
  [3]: https://box.tama.guru
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/06/03/647aded8a9a91.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/06/03/647adef1ce54b.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/27/65b4070845834.jpg
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/06/03/647adf02a5ac4.png
  [8]: https://box.tama.guru/_/tamakyi