---
title: "MIUI刷机那些破事（持续更新）"
categories: [ "记录" ]
tags: [ "MIUI" ]
draft: false
slug: "miuiflashing"
date: "2022-10-05 17:23:00"
---

[scode type="red"]"this MIUI version can’t be installed on this device"[/scode]
这个报错是MIUI12+版本在跨区刷包的时候出现的，电脑上使用MiFlasher进行跨区线刷时，`务必保证选择了“全部删除/保留用户数据”，而非“全部删除并lock”`![image.png](https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/10/15/634a5ac38399f.png)
，目前跨区刷机已经被小米官方限制成仅解bl锁的用户使用，所以当你勾选了“全部删除并lock”的时候，系统会直接无法启动。如果之前手机有绑定账号并且能解锁bl，那么只需要下载小米官方的解锁软件重新再解锁一次就能正常使用了。如果你没绑定账号，那么就去找售后吧。
[scode type="lblue"]"error:Not catch chinkpoint(\$fastboot -s .* lock), flash is not done"[/scode]
这个报错出现在线刷不回锁的时候，字面意思就是在MiFlash软件的Configuration中有一个"Checkpoint"的选项，用于判断刷机脚本是否完成，默认情况下这里是`\$fastboot -s .* lock`，但是如果我们刷机的时候选择“全部删除/保留用户数据”，就不会涉及到上锁的命令，所以会触发这个错误，但其实刷机过程已经完成了，不会对使用有影响，如果觉得碍眼可以把`Checkpoint`给清空，就不会提示了。![image.png](https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2022/10/15/634a5af10a251.png)
[scode type="red"]"我是电信卡，我在欧版上发不出短信"[/scode]
恭喜你刷到的是正版的欧版MIUI。如果非要用这个版本，需要先刷回国内版本的MIUI，进入短信应用用电信卡发一条短信，如果正常能发出再刷欧版，大概率能够解决这个问题。
