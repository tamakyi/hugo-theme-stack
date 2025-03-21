---
title: "捣鼓SteamDeck的那些事"
categories: [ "记录" ]
tags: [ "SteamDeck","archlinux","双系统" ]
draft: false
slug: "steamdeck"
date: "2023-02-02 09:47:00"
---

I have a d○ck.
不得不说，这东西挺有意思的，amd定制的zen2架构CPU及性能相当于1050的显卡能够应付大部分场景使用以及轻度游戏，还能轻松安装SteamOS+WIN10/11组合，玩一些原系统上因为各种原因玩不了的游戏，如果空间不够，还能自己买一个2230的M2换上去。但是在使用过程中这玩意其实坑挺多的，想要快乐玩耍掌握一些linux基础知识以及“合适的网络”是必不可少的。下面是我在使用过程中碰到的一些问题以及解决办法。
####·首次开机联网后一直提示正在安装，然后卡在还剩1秒或者弹窗报错·####
基本上是国内专属问题了，归根结底就是网络原因和"steam.cloud"连接有问题导致更新无法进行，而更新又是必须的就导致了这个死循环。
有网友说我有科技，直接开科技不就行了？如果是在路由的话这必须要设置全局代理，因为SteamDeck的WIFI连接界面不能手动设置代理。用手机开热点或许也是一个好办法，但是实测我的流量根本没走，可能是我的姿势不对吧。
还有网友说在安装界面直接长按电源键硬重启，多几次就能跳过，我试了四五次也不行。其中一次关机重启后进入到一个有LOGO+进度条及网速的界面，也在提示install update，但是更新完自动重启之后还是问题依旧。
目前我唯一测试通过的方法就是手机挂UU加速器（亲测雷神无效）加速steam（仅加速商店是免费的），然后开热点，SteamDeck这边开机的时候要设置为英文+UTC-8时区（即默认设置）再连接热点更新，大概率等待半小时就能通过了，手机这边如果开了流量监控，就会发现流量基本1.5m/s这个速度走（峰值4-5m/s），如果没动的话，建议重启加速器或是点击SteamDeck上的Back返回上一步重新开始，直到流量正常出去。大概也就花费1-2G。（下图是我更新了两次）![2023-02-02T00:43:46.png][1]

####·登录不上Steam账号·####
首次登录可能会遇到这个问题，开加速器即可。

####·我想换SSD，应该注意什么·####
必须换2230长度的M2硬盘替换，一般能买到的都是二手的SN740或者530（530存货比较少反而比较贵），其他长度的硬盘虽然能够魔改替换上去但是会严重影响和硬盘紧贴在一起的电源芯片的散热，有烧鸡可能。详细拆机更换流程可以直接参考[iFixit][2]或者对着[拆机视频][3]操作即可。切记拆机后断开电源再换盘。

####·我想在固态上装双系统，而且开机能够选择引导·####
主要分为以下几个步骤：在U盘上载入一个带gparted的系统-->拆出分区-->装系统-->做引导。
（1）建议找一块USB3.0的U盘，到此处下载[Ventoy][4]最新版本，下载解压执行Ventoy2Disk.exe，插入U盘后点击安装会把P盘数据格式化，然后生成一个Ventoy的空盘，[下载gparted][5]或其他带gparted的系统镜像直接扔进这个空盘，同时准备好WIN10/11的系统镜像，也一并放进去。
（2）把SteamDeck关机，用OTG或拓展坞把U盘连接到SteamDeck。按音量-及电源键一秒进入启动项选择，进入Boot from USB,然后会弹出Ventoy的启动界面并显示所有的镜像，选择下载好的gparted的iso镜像，选择normal启动，然后一路回车就进入镜像了。
（3）选择最大的分区将其resize，在最后部分调整出一块200G的空间，然后新增两个NTFS分区，一个用于装系统（80G），一个用于与SteamOS共享数据及给WIN装游戏。（根据实际情况自行调整）调整完后关机。
（4）按步骤2进入启动项选择，这次选择WIN镜像，后续步骤不再赘述，安装步骤和一般装系统完全一致。
（5）等安装完成后重启进入启动项选择，进入SteamOS并进入桌面模式，在右下角菜单找到系统>终端，
```
passwd  #修改默认密码
sudo passwd  #修改root密码
sudo steamos-readonly disable  #关闭系统只读
git clone https://github.com/jlobue10/SteamDeck_rEFInd
mv SteamDeck_rEFInd .SteamDeck_rEFInd
cd .SteamDeck_rEFInd
sudo mkdir /root/Downloads
sudo chmod +x refind_install_no_pacman.sh
sudo sh refind_install_no_pacman.sh
```
至此引导脚本安装完成，重启之后就能看到StemaOS和WIN的两个LOGO了，五秒内不选择默认从SteamOS启动。之所以选择`refind_install_no_pacman.sh`是因为SteamOS默认情况下要开启pacman非常麻烦，而且后续升级的话pacman安装的相关软件都会丢失仅保留配置。

####·我想玩学习版游戏·####
自测了几个proton版本以及proton next还有heroics game launcher，还是有很多游戏没办法正常启动，建议直接上WIN系统。

####·WIN系统设备管理器里面安装了Steam官方的驱动之后还是有几个感叹号·####
截止2023.2.2，Steam官方仅提供了APU（显卡+声音）、声卡、蓝牙、读卡器、WIFI驱动，一部分驱动在WINDOWS系统更新后自动安装，剩余的部分驱动等待官方跟进放出。

####·进入gparted镜像时报错无法进入图形界面·####
如果提示`I/O error, dev mmcblk0, sector x op 0x0:(READ) flags 0x0 phys_seg`，请检查是否插入了SD卡，如果有，先拔出再重新进入。

####·steam内游戏下载速度慢·####
去设置看看下载服务器是否本地位置，不然就开加速器（例如我进系统他自己给我调整到日本札幌，换回国内重启后速度就上来了）。

####·桌面模式的discovery商店访问速度慢·####
Steam桌面商店使用的是flathub源，默认使用的是`https://glathub.org/repo`，在国内访问较慢，建议换到上海交大源：
```
sudo steamos-readonly disable
sudo flatpak remote-modify flathub --url=https://mirror.sjtu.edu.cn/flathub
```
然后进商店里面更新一下就好了。

####·我账号很多想用家庭共享，但是好像不起作用·####
因为家庭共享是基于设备的，只在电脑上开通在deck上市无法启用的。这种情况下只能先登陆需要共享的账号，下载个游戏，然后登陆另外一个账号，这里会显示成“借用”状态，点击申请授权即可。

####·怎么进BIOS/启动菜单·####
关机状态下：
`音量`+ & `电源键` 一秒（有提示音就放手） = `BIOS设置`
`音量`- & `电源键` 一秒（有提示音就放手） = `引导选择`

####·怎么使用Steam自带的Proton环境·####
在桌面模式下进入Steam，选择添加非Steam游戏，对话框默认之能选择`.desktop`修改成`all files`，然后选择需要执行的`.exe`文件，保存后右键Steam中生成的项目选择属性，兼容性设置中勾选强制使用，然后选择任意一个已安装的Proton环境，再运行即可。

####·Steam自带的Proton在哪个路径·####
以Proton7.0为例，其目录位于`/home/desk/.local/.(忘了，待查)./driver_c/`

####·连接了其他设备切换模式后还能用吗·####
亲测可以。以蓝牙设备为例，在游戏模式下连接键鼠后切换到桌面模式能正常使用。

（未完待续）

  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/02/02/63db0743c8f68.png
  [2]: https://zh.ifixit.com/Guide/Steam+Deck+SSD+%E6%9B%B4%E6%8D%A2/148989
  [3]: https://www.acfun.cn/v/ac39982689
  [4]: https://github.com/ventoy/Ventoy/releases
  [5]: https://downloads.sourceforge.net/gparted/gparted-live-1.4.0-6-amd64.iso