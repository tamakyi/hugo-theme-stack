---
title: "记一次KPW3拆机降级"
categories: [ "记录" ]
tags: [ "kindle","KPW3","越狱" ]
draft: false
slug: "264"
date: "2023-07-23 22:40:00"
---

> "亚马逊将于一年之后即2023年6月30日，在中国停止Kindle电子书店的运营。在此之后，您将不能购买新的电子书。对于已经购买的电子书，您可以在2024年6月30日之前下载，并且可以在此后继续阅读。"

以上公告来自亚马逊。这意味着6月30日起Kindle的国内线上服务将不再完整，需要完整的服务需要申请一个其他地区的亚马逊账号，但是国外的亚马逊商城中文书籍极少甚至有些地区没有，趁这个机会我就想把手头的KPW3越个狱。
之所以要拆机是因为KPW3当前免拆机越狱的最后一个版本是5.14.2（来源：[Kindle 越狱支持一览：检查 Kindle 设备能否越狱][1]），而我的设备已经升级到5.15.1.1,想要降级越狱必须拆机。

准备工作：

 - 杜邦线公对母若干根
 - TTL刷机板（我用的是FT232RL，这玩意能选择输出1.8 3.3或者5伏）
 - 拆机工具（包括但不限于翘片、螺丝刀、最好有热吹风）
 - 电烙铁（选配，但是最好是有）
 - SecureCRT

拆机过程不再赘述网上一抓一大把，拿出主板的时候注意从顶部抠开。
![底部的充电口和电源会有卡槽，因此选择顶部拆除主板][2]
安装好TTL的驱动并确定好COM端口号，配置SecureCRT如下图所示。
![我的端口是COM4，SecureCRT配置一定要如图所示设置][3]
接下来进行硬件连接，需要切换成3.3V模式，然后划出三根线，GND连接主板上任意部位，我连接到充电口；DEBUG接口左边为RX，连接到TTL的TX，中间为TX，连接到TTL的RX，最终如下图所示。
![接线方式][4]
*正式刷写时此处应用烙铁飞出RX TX这两根线并固定好，否则刷到半断开了容易GG。
此时重启KPW3，SecureCRT上应该会显示出一些信息，证明是连接成功了。
![SecureCRT信息][5]

剩下的部分等我的烙铁到了再更新 ::acfun:11:: 。


----------
焊点太小了焊不上，此贴终结。


----------
买了个J焊头，这次焊上去了，继续更新。因为触点实在太小了，拉了两坨锡把触点搞大，好在没有连锡。
![2023-07-30T11:46:40.png][6]
长按电源10S重启，直到SecureCRT信息跳动，疯狂的按Enter（其实只要在`Hit any key to stop autoboot:0`之前按住就行了，不然会进入正常启动流程。
![2023-07-30T11:48:09.png][7]
然后电脑安装这个[驱动][8]，驱动选择`libsub-win32`即可。
![驱动安装][9]
驱动安装完成后回到SecureCRT,在`uboot>`后输入`run bootcmd_diags`回车，然后再次疯狂回车，避免程序跑完后系统正常启动。注意，*此时要把USB数据线与电脑连接。*
![进入Recovery模式][10]
如果成功了，会弹出一个Menu菜单，这个菜单会有倒计时，10秒没有反应就正常启动，因此要快点按住`e`，此时电脑会弹出一个Kindle的分区，把`update_pw3_factory_5.8.9.0.1.bin`这个固件放进去，复制完成后按住`1`，系统会进入恢复模式并自动升级。
![复制到kindle][11]![复制到Kindle2][12]![开始升级][13]
在Kindle上可以看到显示正在安装更新。
![正在安装更新][14]
这个时间是比较久的，而且线路松动的话设备很容易寄，因此焊接必须要做好，保证线路不能松动。
等大约二十分钟后系统自动重启，可以看到系统版本已经降下来了。
![降级成功][15]


  [1]: https://bookfere.com/post/406.html
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/23/64bd38ff776cb.png
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/23/64bd39ced253e.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/23/64bd3b5343c36.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/23/64bd3c4f9ca10.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c64da366c22.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c64dfb2e960.png
  [8]: https://359303267.github.io/files/kindle-02/Zadig.zip
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c654fdd287a.png
  [10]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c655b86b3a3.png
  [11]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c656bd55ca9.png
  [12]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c656d7ad5cd.png
  [13]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c657074ccc8.png
  [14]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c6575d5d9ee.png
  [15]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/30/64c65790d27fa.png