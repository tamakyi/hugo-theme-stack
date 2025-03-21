---
title: "记一次VBios的升级和resizebar的开启"
categories: [ "记录" ]
tags: [ "3060ti","vbios","sizebar" ]
draft: false
slug: "3060ti_vbiosupdate_sizebar"
date: "2023-05-07 23:28:00"
---

首先科普一下resize bar是什么：

> 可调整大小的 BAR 是一种可选的 PCI Express 接口技术。当您在游戏中的世界中移动时，GPU 内存 （VRAM） 会通过许多小型 CPU 不断传输纹理、着色器和几何体到 GPU 的传输。随着现代游戏资产规模的不断增长，这导致了大量的转移。使用可调整大小的 BAR，可以根据需要请求资产并完整发送，因此 CPU 可以有效地访问整个帧缓冲区。如果发出多个请求，传输可以同时进行，而不是排队。（来自[Nvidia官网][1])

这不是今天刚买了DG1嘛，有说到什么Resizebar什么新功能巴拉巴拉的，之前听都没听过，查了下资料发现好像是有点用处的嗷，又看了下30系确实是能用的，于是喜滋滋的去[这里][2]看如何操作（这里的七彩虹链接404了，后面是去官网找的）。我的卡是七彩虹的iGame GeForce RTX 3060 Ti Ultra W OC-V，去官网上找到对应的驱动页面，找到BIOS看到一个3060Ti Resizable BAR Support，哟还是今年三月更新的，赶紧下载了。
![显示更新时间还挺近的][3]
打开压缩包之后打开`3060tiRZBARupdaterV2.exe`（这里显示修改时间2021/4/9，怪）
![3060tiRZBARupdaterV2.exe][4]
按y进行下一步，发现提示`Unsupported by this tool, contact card vendor` ::acfun:1004:: 这不是我的卡的驱动吗？怎么还能不支持的？然后再搜了下，开启resize bar需要满足以下条件：

 - BIOS中支持并开启`Resize Bar Support`同时开启`Above 4G Decoding`
 - 关闭CSM（其实我的主板在打开`Resize Bar Support`后自动就把CSM关闭了，好像CSM和这个功能有冲突）
 - 一张30系显卡

然后我就走了一遍流程把上面都打开了（顺便还把BIOS给升级了一遍）。重启后发现还是显示`Resize Bar Disable`
![这是最后处理好了的图，原来这里是显示Disable的][5]
然后在GPU-Z的Advanced-PCIe Resizable BAR下面的Resizable BAR enabled in BIOS显示是NO ::acfun:19:: 
![这里原来一直都是NO][6]
什么勾八？
网上搜索了一下，发现也有人出现这个情况，然后他把CSM重新打开就能识别了。我复现了一下发现根本不可行，而且其他例如CSM disabled也变成NO了，这不是妥妥的坑人嘛。
本来我都打算放弃了，在这个[贴子][7]上看到有提到vbios的问题，然后我琢磨着是不是我的vbios版本不对，然后去查了下如何升级vbios以及去 哪儿整vbios。
[button color="danger"]风险警告！刷写VBIOS操作不当可能会导致显卡变砖！[/button]
[button color="danger"]风险警告！刷写VBIOS操作不当可能会导致显卡变砖！[/button]
[button color="danger"]风险警告！刷写VBIOS操作不当可能会导致显卡变砖！[/button]
简单来说，就是到[这个网站][8]搜索到自己型号的显卡，找到之后下载到一个rom后缀的文件，这个就是vbios了。
![我对应的型号是这个][9]
文件有了，还需要工具，也是到这个网站找到NVIDIA NVFlash这个页面:[https://www.techpowerup.com/download/nvidia-nvflash/][10]按自己机器的型号下载到压缩包之后，把文件解压，并把rom文件放在同一个文件夹。
![如果怕ROM文件混淆可以自己重命名][11]
以管理员身份运行CMD，并进入此文件夹。
![CMD进入文件夹][12]
先执行`nvflash.exe --list`查询是否识别到显卡并且判断显卡vbios版本
![此处的10DE,2486,7377,1501便是我的vbios版本，当然，这已经是升级后的了][13]
然后执行`nvflash.exe -6 Colorful.RTX3060Ti.8192.201112.rom`这里选择要升级的rom文件，`-6`参数是强制升级。
![升级过程，没截图，手机拍屏幕][14]
上图中现有版本是94.04.27.80.EC，要升级的版本是94.04.27.80.ED，看上去确实是差了一个版本。
如果顺利的话这里直接一路y过去，最后会提示你要重启，重启就是了。
然后再次打开上面那个`3060tiRZBARupdaterV2.exe`，哦豁。
![已经提示支持升级了][15]
`Update supported, will be applied` 我直接一个y。
![升级成功了][16]
提示`Update successful`，它要求重启，那我直接一个重启。
最后就是喜闻乐见的开启成功了，果然是vbios的问题。
![BAR1原来只有256，现在变成8192，即全卡显存大小][17]
完事手工。 ::acfun:06:: 
[button color="danger"]再次警告！刷写VBIOS操作不当可能会导致显卡变砖！[/button]


  [1]: https://www.nvidia.com/en-us/geforce/news/geforce-rtx-30-series-resizable-bar-support/
  [2]: https://nvidia.custhelp.com/app/answers/detail/a_id/5165/~/nvidia-resizable-bar-firmware-update-tool
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457ba25bbd51.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457ba8cdc288.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457bc5cdc0f7.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457bcba7a168.png
  [7]: https://tieba.baidu.com/p/8092717058
  [8]: https://www.techpowerup.com/vgabios
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457be6670682.png
  [10]: https://www.techpowerup.com/download/nvidia-nvflash/
  [11]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457bf0d1451d.png
  [12]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457bf5d1318d.png
  [13]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457bfafd2aa7.png
  [14]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457c038e046c.png
  [15]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457c1b0845eb.png
  [16]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457c15f8e864.png
  [17]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457c23aed0ea.png