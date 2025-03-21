---
title: "记录windows各类错误及尝试进行解决的流程"
categories: [ "记录" ]
tags: [ "windows","系统错误","系统更新" ]
draft: false
slug: "windows-error-fix"
date: "2024-01-31 09:51:00"
---

在基本上尝试了各类Windows系统我得出一个结论，那就是没有完美的Windows系统，除了Win7、Win10 LTSC、Win Server 2022这几个屁事比较少之外，其他总能碰到奇奇怪怪的事情，在此做个文章记录发生过的问题以及记录一些尝试解决处理过程中碰到的事情，免得后面又忘了又翻一遍材料。


<!--more-->

### 系统更新类
#### 0x80070643 - 0x8024200B
在什么系统出现：Windows 10 企业版 LTSC（21H2，19044.3930）
什么时候出现：系统更新
提示什么：安装失败: Windows 安装下列更新失败，错误为 0x8024200B: 2024-01 适用于 Windows 10 Version 21H2 安全更新，适合基于 x64 的系统 (KB5034441)。
是否解决：未知
怎么解决：

 - 尝试1：手工打补丁：到[https://catalog.update.microsoft.com/][1]查找补丁号`KB5034441`，结果为`We did not find any results for "KB5034441"`，不成功。
 - 尝试2：查找官网信息：到MS官网查找[关于`KB5034441`的信息][2]，找到前面的这条记录提到如下内容：

> IMPORTANT

> This update requires 250 MB of free space in the recovery partition to install successfully. If the recovery partition does not have sufficient free space, this update will fail. In this case, you will receive the following error message: 

> 0x80070643 - ERROR_INSTALL_FAILURE 

> To avoid this error or recover from this failure, please follow the Instructions to manually resize your partition to install the WinRE update and then try installing this update.

> 看起来需要先保证必须在恢复分区中具有 250 MB 的可用空间才能成功应用此更新。

用DG看了下我的分区结构，发现根本没有Winre这个分区，笑死。
![我的分区结构，其中分区3-4是给debian用的][3]
但上面也有提到：
> 如果 PC 没有恢复分区，则不需要此更新。在这种情况下，可以安全地忽略该错误。

那就只能当这个问题不存在了。 

### 内部错误状态类
#### 10013
在什么系统出现：Windows Server 2022
什么时候出现：正常使用时出现
提示什么：创建 TLS 客户端 凭据时发生严重错误。内部错误状态为 10013。
是否解决：否
怎么解决：打开运行，输入`inetcpl.cpl`后进入`Internet选项`，点击`高级`选项卡，找到`使用SSL 3.0`，取消勾选保存并重启，之后错误数量明显减少。
出现原因：系统已经不支持SSL3.0及之前版本的安全协议，系统默认是没有勾选这一项的，但是可能是安装了某些软件把该选项打开了导致报错。



  [1]: https://catalog.update.microsoft.com/
  [2]: https://support.microsoft.com/en-us/topic/kb5034441-windows-recovery-environment-update-for-windows-10-version-21h2-and-22h2-january-9-2024-62c04204-aaa5-4fee-a02a-2fdea17075a8
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/31/65b9a73b0cd30.png