---
title: "Win11安装优化那些事"
categories: [ "记录" ]
tags: [ "WIN11" ]
draft: false
slug: "win11_new_setup"
date: "2022-11-06 23:08:00"
---

坚持了大半年的WIN10今天终于因为不明原因无法启动了（开机无限转圈圈，进入修复模式修复失败无解），于是干脆就把系统换成Win11。自从上次直接从10升11出现一堆bug之后，~~以及我对阿三一直以来抱有的一种不信任感~~让我这半年基本没摸过11，这次就全新安装，给它个面子，看看它能不能给我个面子。


<!--more-->

[scode type="lblue"]安装信息[/scode]
使用的是22H2的消费者版本镜像。安装选择的是Win11专业工作站版。
磁力链：`magnet:?xt=urn:btih:85730cfa4be6b8c3e655d76becc2f6d32f261ce4&dn=zh-cn_windows_11_business_editions_version_22h2_updated_oct_2022_x64_dvd_b4e3a4f8.iso&xl=5515886592`

[scode type="lblue"]激活办法[/scode]
激活还是老样子，可以用KMS激活，或者直接使用[AAct][2]进行激活。
[scode type="yellow"]激活注意事项[/scode]
 下载AAct之前需要把Windows安全中心的“病毒和威胁防护”相关设定先关闭，否则会被删除。卡巴斯基反病毒会检测到`HEUR:HackTool.Win32.KMSAuto.gen`，反正看你个人。![2022-11-06T14:58:45.png][3]

[scode type="lblue"]让任务栏左对齐，使“开始按钮居左[/scode]
右键任务栏，选择任务栏设置，在”任务栏行为>任务栏对其方式选择“靠左”。
![2022-11-06T15:04:03.png][4]

[scode type="lblue"]让右键菜单以传统方式显示[/scode]
Win11最影响体验的改变就是把右键菜单放到第二层，非常浪费时间。以下方法在22H2实验后仍然有效：
以管理员方式运行cmd，并输入
`reg add HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32 /ve`
回车后重启资源管理器即可。
![2022-11-06T15:07:20.png][5]

以下是目前做到的效果，后续有变动再更新。
![2022-11-06T15:08:26.png][6]


  [1]: magnet:?xt=urn:btih:85730cfa4be6b8c3e655d76becc2f6d32f261ce4&dn=zh-cn_windows_11_business_editions_version_22h2_updated_oct_2022_x64_dvd_b4e3a4f8.iso&xl=5515886592
  [2]: https://www.nite07.com/aact/
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/06/6367cba685a30.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/06/6367cce4d538e.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/06/6367cda9357d0.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/11/06/6367cdebb7be8.png