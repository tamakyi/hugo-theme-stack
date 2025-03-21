---
title: "[插件推荐]github项目屏蔽插件"
categories: [ "软件推荐" ]
tags: [ "github","屏蔽" ]
draft: false
slug: "PurifyGithubSearchPage"
date: "2024-02-17 21:46:00"
---

有时候在github上搜索，总会搜出一些不是很友好的东西（至于是什么自己随便搜一下就知道了），这些人通常会在readme里面添加一些常用的关键词，然后经常性的更新，让项目看起来“很热门”。但其实那是什么东西懂的都懂。
**你们不玩zz会死我不管，别污染别人眼睛。**


<!--more-->


可以使用以下这个项目[PurifyGithubSearchPage][1]，安装油猴之后直接新建一个脚本，把[https://raw.githubusercontent.com/wilson7yz/PurifyGithubSearchPage/main/fuckDinner.js][2]的内容复制进去保存再刷新就好了。
或者到油猴上[Github搜索净化][3]直接点击安装，效果一样。
如果要新增屏蔽的用户名，直接把在源码的这个地方新增就行了。
```
const dinnerNames = ["xxx"];
```
效果图：
使用前：
![使用前][4]
使用后：
![使用后][5]


  [1]: https://github.com/wilson7yz/PurifyGithubSearchPage
  [2]: https://raw.githubusercontent.com/wilson7yz/PurifyGithubSearchPage/main/fuckDinner.js
  [3]: https://greasyfork.org/zh-CN/scripts/473912-github%E6%90%9C%E7%B4%A2%E5%87%80%E5%8C%96
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/02/17/65d0b99ac2876.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/02/17/65d0bc62d9eb2.png