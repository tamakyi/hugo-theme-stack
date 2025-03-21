---
title: "Vultr免费服务器体验"
categories: [ "记录" ]
tags: [ "vultr","云服务器" ]
draft: false
slug: "vultr-free-instance"
date: "2023-07-16 01:35:27"
---

今年年初的时候Vultr推出了免费服务器计划，只要满足以下条件就能申请一台服务器：

 - 绑定了信用卡
 - 开启2FA

服务器的规格如下：

 - 单核虚拟处理器
 - 0.5GB内存
 - 10GB磁盘
 - 2TB的免费全球带宽

0.5G内存可能是小了点，但是免费的，跑个Debian应该还行。
申请之后一段时间会收到这个提示：
![2023-07-15T16:52:09.png][1]
其中有提到这个计划的启动时间和结束时间，但是有网友问过客服，如果到期了再次审核满足条件的话是能继续续下去的。
建立新实例的步骤如下：

 1. 选择Cloud Compute

![D1][2]

 - 选择Regular Performance
![D2][3]

 - 选择Server Location。需要注意的是，免费计划只适用于美国西雅图、迈阿密和德国法兰克福，选择其他地方是不会在Server Size出现"Free Tier"这个选项的。
![D3][4]![D4][5]
新建成功之后去PING了一下IP，居然是通的 (ﾟ∀。)，记得几年前Vultr家的服务器是一封一大片。

 - 磁盘占用情况：
```
root@vultr:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            207M     0  207M   0% /dev
tmpfs            46M  696K   46M   2% /run
/dev/vda1       9.4G  2.6G  6.3G  30% /
tmpfs           229M     0  229M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs            46M     0   46M   0% /run/user/0
```
还剩6.3G，还行，还能做些事情。

 - 内存占用情况：
```
root@vultr:~# free -m
               total        used        free      shared  buff/cache   available
Mem:             457         104         233           0         140         353
Swap:              0           0           0
```
还剩233M...这下不开swap不行了，给swap划分1个G，那就还剩5个G左右的空间。
还要配置好swap的使用机制：
```echo "vm.swappiness = 50" >> /etc/sysctl.conf```
这个数值0即完全不使用swap，100即更偏向使用swap。这么小内存的机子，我就配置个五五开差不多了。

 - 网速测试：
```
root@vultr:~# speedtest-cli
Retrieving speedtest.net configuration...
Testing from Choopa, LLC (********)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Wowrack (Seattle, WA) [1.31 km]: 9.041 ms
Testing download speed................................................................................
Download: 655.26 Mbit/s
Testing upload speed......................................................................................................
Upload: 3234.36 Mbit/s
```
 (ﾟ∀。) Really nigger?
拖个镜像试试看
```
root@vultr:~# wget https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/debian-12.0.0-amd64-DVD-1.iso
--2023-07-15 17:24:53--  https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/debian-12.0.0-amd64-DVD-1.iso
Resolving cdimage.debian.org (cdimage.debian.org)... 2001:6b0:19::163, 2001:6b0:19::173, 2001:6b0:19::165, ...
Connecting to cdimage.debian.org (cdimage.debian.org)|2001:6b0:19::163|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://saimei.ftp.acc.umu.se/debian-cd/current/amd64/iso-dvd/debian-12.0.0-amd64-DVD-1.iso [following]
--2023-07-15 17:24:54--  https://saimei.ftp.acc.umu.se/debian-cd/current/amd64/iso-dvd/debian-12.0.0-amd64-DVD-1.iso
Resolving saimei.ftp.acc.umu.se (saimei.ftp.acc.umu.se)... 2001:6b0:19::138, 194.71.11.138
Connecting to saimei.ftp.acc.umu.se (saimei.ftp.acc.umu.se)|2001:6b0:19::138|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3931095040 (3.7G) [application/x-iso9660-image]
Saving to: ‘debian-12.0.0-amd64-DVD-1.iso’

debian-12.0.0-amd64-DVD-1.iso                                100%[===========================================================================================================================================>]   3.66G  87.3MB/s    in 46s     

2023-07-15 17:25:41 (81.9 MB/s) - ‘debian-12.0.0-amd64-DVD-1.iso’ saved [3931095040/3931095040]
```
欸欸欸 (ﾟ∀。)



  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/16/64b2ceba25477.png
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/16/64b2cf27838d7.png
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/16/64b2cf6e27c7a.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/16/64b2d0139c2d4.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/07/16/64b2d0213b511.png