---
title: "Switch15.0.1大气层系统升级的记录（整合包）-更新到17.0.1"
categories: [ "记录" ]
tags: [ "大气层","switch","Atmosphere" ]
draft: false
slug: "switch_Atmosphere"
date: "2022-11-27 01:15:00"
---

目前系统是已经硬破的状态，且能正常运行，但是版本已经有点老了（Atmosphere版本14），导致目前有部分新的游戏无法运行，因此在此记录Switch升级大气层系统的相关步骤。


<!--more-->

事前准备：

 - 一台Switch，已经硬破（软破需要看ShallowSea的说明文档，会多出来两个步骤）
 - 确保Switch有足够的电。。。
 - Switch固件，[下载地址][1],当前最新15.0.1
 - ShallowSea整合包，[下载地址][2]，当前最新2.12.4，相关说明可以看[这里][3]。下载地址会标注整合包支持的系统版本。
<scode type=red>请注意，ShallowSea因DMCA已经被屏蔽，其他固件请参考https://www.gamer520.com/48671.html</scode>


<!--more-->
下面开始升级。

 - 取出Switch上的SD卡，删除除了`emmMMC` `Nintendo` 和你保存游戏的文件夹之外的其他所有文件。
![2022-11-26T16:39:02.png][4]
 - 把ShallowSea整合包`ShallowSea-ams.zip`解压覆盖到SD卡上。
![2022-11-26T16:39:41.png][5]
 - 在SD卡上新建一个`firmware`文件夹（或者其他英文文件夹），把Switch固件解压其中。
![2022-11-26T16:09:39.png][6]
 - 确认文件完整后弹出SD并插入Switch上。
 - 插入注入器和短路器，"+"与电源键蓝灯进入引导
正常进入系统，设置里的主机-更新主机的Atmosphere版本已经升级到了最新1.4.0
![2022-11-26T17:08:12.png][7]
 - 接下来升级系统，进入相册-Daybreak
![2022-11-26T17:09:14.png][8]
 - 选择Install，并进入之前下载了15.0.1固件的文件夹`Firmware`，弹出Update infomation等待验证完成后选择`Continue`,Select driver variant选择`Install(FAT32+exFAT)`,等待升级完成后选择`Reboot`重启。
![2022-11-26T17:11:07.png][9]
![2022-11-26T17:11:32.png][10]
![2022-11-26T17:13:39.png][11]
可以看到系统版本也更新了。
![2022-11-26T17:15:01.png][12]


----------


上面的作者整合包Github被DMCA了，目前可以考虑[https://github.com/Yuanbanba/Atmosphere][13]这个版本，更新方法一样，保留好SD卡内的Emutendo和Nentendo文件夹（如果没有了也不要紧），其他删掉，再把整合包解压覆盖到SD卡根目录，此包支持系统版本为最高17.0.1，更新方法和上面一样。
为避免惨剧再次发生，以上文件已在云盘做备份：[https://share.tama.guru/AliDisk/Switch_Games/ROM/17.0.1][14]


  [1]: https://darthsternie.net/switch-firmwares/
  [2]: https://github.com/carcaschoi/ShallowSea
  [3]: https://github.com/carcaschoi/ShallowSea/blob/main/docs/User%20guide.md
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/638241260e32a.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/6382414d42649.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/26/6382365a2312d.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/638247fc998d5.png
  [8]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/6382483aa25e9.png
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/638248ab215ba.png
  [10]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/638248c432267.png
  [11]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/638249433066c.png
  [12]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/27/638249951880a.png
  [13]: https://github.com/Yuanbanba/Atmosphere
  [14]: https://share.tama.guru/AliDisk/Switch_Games/ROM/17.0.1