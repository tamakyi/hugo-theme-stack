---
title: "[好软推荐]Winlator 在安卓上运行exe程序的更好方案"
categories: [ "软件推荐" ]
tags: [ "Winlator","wine","安卓","虚拟" ]
draft: false
slug: "Winlator"
date: "2023-11-12 13:51:00"
---

从安卓4.x时代开始我便开始想着能不能在手机上运行一些电脑上的游戏，比如红色警戒还有某些Galgame，但是受制于当时的硬件机能，exgear这类的软件运行起来效果并不好，最直观的体现就是卡顿，或者就是直接跑不起来。网上流传的exgear版本非常多，就算放到现在实际体验下来也经常碰到这样那样的毛病。在体验了几天Winlator之后，感觉相对于exgear更稳定一些，偶尔有闪退（我用的鸿蒙4.0，不知道有没有这方面的关系），但还是挺不错的，出门在外想推点小游戏也不用带笨重的掌机了。


<!--more-->

### 项目地址
[winlator][1]

### 项目介绍
Winlator 是一款 Android 应用程序，可让您使用 Wine 和 Box86/Box64 运行 Windows（x86_64）应用程序。

### 安装方法
 - 直接到[Github][2]下载最新版本的安装包和对应数据包，如最新版本的是2.0，对应的数据包是main.2.com.winlator.obb，如果下载速度太慢，可以到[狼的共享盘][3]下载。（原版是英文的，但是不影响使用） 20240819更新7.1汉化整合包：[AliShare][4]
 - 先安装Winlator2.0-UPDATED_f26.apk，安装完成后打开程序，会闪退并提示："obb数据包不存在"，然后需要安装Solid Explorer这个文件管理器（很多文件管理器在安卓13之后就无法访问Android文件夹了，这会影响后续的操作），进入`内部存储器>ANDROID>OBB>COM.WINLATOR`文件夹，把下载下来的main.2.com.winlator.obb复制到这里（过程中会提示是否允许授予允许管理所有文件的权限，这里选允许）。
 - 重新打开Winlator程序，这时会提示正在安装正在安装obb映像，通常一分钟左右就完成了。

### 配置方法
1.在容器管理页面右上角点击加号进入创建新容器页面；
2.容器名称随意设置；
3.分辨率根据自己的手机选择，如果手机分辨率1920*1080的话建议使用1280*720分辨率，不然字体很小；
4.Wine版本一般选择默认即可，如果后续运行程序出问题可以使用其他版本试试，或者自己安装其他Wine版本，此处不详细说明。
5.显卡驱动分为LLVMpipe(Software) Turnip+Zink和VirGL三种，按网上的说法Vrigl版兼容性更好，Turnip+Zink版速度快（但是turnip只支持部分高通gpu6的手机），LLVMpipe使用软件编解码，貌似占用资源更高，新手机建议用Vrigl减少兼容性问题。
6.Box版本保留默认即可。
7.处理器核心开关根据自己的手机选择，麒麟9000s默认只开CPU4-7，我保留这个配置使用无异常。
![创建新容器][5]
8.WINE注册表项和DX组件基本上只需要保留默认。
![WINE注册表项][6]
![DX组件][7]
9.环境变量这里如果游戏乱码可以设置LC_all这一项，也可以在容器内设置，其他保留默认即可。
![环境变量][8]
10.盘符默认把存储器下的Download文件夹（即下载默认目录）设置成D:，建议新建个文件夹来专门放置游戏并配置盘符。
![驱动器（盘符）][9]
11.高级设置也只需要保持默认即可。
![高级][10]
配置完成后点击右下角的✔，返回到主界面找到刚刚生成的容器，选择右边的三个点点击运行容器就跑起来了。

### 注意事项

 - 建议配置完成后安装[常见运行库包][11]以及[中文字体补丁][12]，安装运行库需要10分钟左右时间，安装中文字体补丁时可能会提示需要安装Wine Geckc，按要求点击安装即可，如果WiFi下载慢建议用流量下载。
![运行库和字体补丁安装][13]

### 使用截图
![截图1][14]
![截图2][15]


  [1]: https://github.com/brunodev85/winlator
  [2]: https://github.com/brunodev85/winlator/releases
  [3]: https://share.tama.guru/AliDisk/%E8%BD%AF%E4%BB%B6%E5%88%86%E4%BA%AB/Winlator
  [4]: https://share.tama.guru/AliShare/Winlator_7.1_f63.apk
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/65505f763eeec.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/6550648240862.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655064bc36369.png
  [8]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655064dc4f24e.png
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655065379f824.png
  [10]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655065916c6c0.png
  [11]: https://share.tama.guru/AliDisk/%E8%BD%AF%E4%BB%B6%E5%88%86%E4%BA%AB/Winlator/setup.exe
  [12]: https://share.tama.guru/d/AliDisk/%E8%BD%AF%E4%BB%B6%E5%88%86%E4%BA%AB/Winlator/Chs%20Fonts(zh_CN).exe
  [13]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655066d472356.png
  [14]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655067b1d3705.png
  [15]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/12/655067bee434d.png