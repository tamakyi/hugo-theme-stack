---
title: "更换了tama.host的模板，现在更像个导航页了"
categories: [ "默认分类" ]
tags: [  ]
draft: false
slug: "tama-host-remake"
date: "2023-10-28 01:03:08"
---

导航站的地址不变，依旧是[https://tama.host/][1]，只不过原来的版本就只有一个搜索框（其实右侧可以打开导航列表，但是不明显），而且当时脑子一抽用了黄黑做网站logo，我在外面打开自己的网站都脸红！
而新的导航页基于[webstack-go项目][2]，界面简洁干净，且可以选择后台添加内容（纯静态版本就需要手动修改html，上班时间我是打开一个小小的窗口一点点的改，里面天量的div一不小心就错删）。
![首页][3]
现在我所做的修改包括：

 - 把部分静态资源文件扔到OSS上。目前主要是CSS和JS文件以及部分的图片，字体因为阿里云跨域问题保留使用fontawesome和lineicons，至于图标，等以后添加得差不多了再上传。
 - 原来本站的匿名提问箱使用的部分资源位于tama.host下，更换之后这部分无法访问（具体就是10月26日晚至27日中午访问提问箱会出现首页弹窗报错，部分内容不显示），现已新增二级域名并迁移。
 - 把我edge浏览器上杂七杂八的标签整理汇总并分享，后续有好东西会慢慢增加。

  [1]: https://tama.host/
  [2]: https://github.com/ch3nnn/webstack-go
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/28/653beb5662d21.png