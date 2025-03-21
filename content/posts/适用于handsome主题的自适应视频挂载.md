---
title: "适用于handsome主题的自适应视频挂载"
categories: [ "记录" ]
tags: [  ]
draft: false
slug: "handsome-framevideo"
date: "2022-07-24 01:59:00"
---

原文请访问：[button type="round" color="success" url="https://www.raycns.com/archives/774.html"]此处[/button]


<!--more-->

适用于带有iframe功能的视频网站。
使用方法：
将以下代码添加至 Handsome主题外观>设置外观开发者设置>自定义CSS
```
/*视频挂载*/

.iframe_video {
position: relative;
width: 100%;
}

@media only screen and (max-width: 767px) {
.iframe_video {
height: 15em;
}
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
.iframe_video {
height: 20em;
}
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
.iframe_video {
height: 30em;
}
}

@media only screen and (min-width: 1200px) {
.iframe_video {
height: 40em;
}
}

.iframe_cross {
position: relative;
width: 100%;
height: 0;
padding-bottom: 75%
}

.iframe_cross iframe {
position: absolute;
width: 100%;
height: 100%;
left: 0;
top: 0
}
```

演示

打开你想挂载在网站的视频
复制视频源代码复制嵌入代码
在复制的嵌入链接的iframe与src之间添加class="iframe_video"

以下演示
```
<iframe class="iframe_video" src="https://www.acfun.cn/player/ac35737823" id="ACPlayer-re" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>
```
成片：
<iframe class="iframe_video" src="https://www.acfun.cn/player/ac35737823" id="ACPlayer-re" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>