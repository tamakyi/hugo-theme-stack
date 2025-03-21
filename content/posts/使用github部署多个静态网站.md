---
title: "使用github部署多个静态网站"
categories: [ "记录" ]
tags: [  ]
draft: false
slug: "github_muti_pages"
date: "2023-10-25 23:16:29"
---

因为有时候需要部署一些静态网站，但是自己的服务器上网站实在太乱了，部署在github便是我最好的选择。
默认情况下是只能部署一个网站的，如果需要部署多个就需要自己动手操作一下。


<!--more-->


操作的大前提是已经部署好自己用户名的网站仓库，例如我的就是[tamakyi.github.io][1]。
之后再创建一个repository，此处的名称就会是之后的二级域名，例如我的Repository name是index，之后这个网站的地址就是[https://tamakyi.github.io/index][2]。除了Repository name之外，其他都可以保留默认，然后选择Create repository.
![repository setting][3]
然后要做的就是把本地的网页文件部署上去，因为在Windows环境ixa，为了直观的体现，此处我安装了Github Desktop，在创建完repository之后点击Set up in Desktop跳转到Github Desktop。
![Set up in Desktop][4]
然后Locale path选择要clone到本地的目录，例如C:/wwwroot/。
![Locale path][5]
之后该目录下会形成一个index文件夹，然后把静态网站所有文件全部扔上去（注意如果有引用文件记得修改），一定要确保该目录下有index.html文件，能且仅能用html格式。
此处我只放了一个index.html文件，然后在Github Desktop上选择Commit to main并点击Publish branch。
![选择Commit to main并点击Publish branch][6]
然后在网页端打开仓库，进入setting。
![Open repository][7]
在Pages页面把Branch的None切换到main，然后点击save。
![Pages setting 1][8]
![Pages setting 2][9]
等待几分钟，然后Pages页面就出现网址了。
![Create success][10]
也能正常打开了。
![正常打开][11]


  [1]: https://tamakyi.github.io
  [2]: https://tamakyi.github.io/index
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/65392cc5cab91.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/65392d9a9dbb4.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/65392e2d140da.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/65392fe60742f.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/6539303c29146.png
  [8]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/6539309bc201e.png
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/653930ac32fdb.png
  [10]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/653931096978e.png
  [11]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/10/25/653931279ab72.png