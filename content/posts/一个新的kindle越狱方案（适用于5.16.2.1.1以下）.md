---
title: "一个新的kindle越狱方案（适用于5.16.2.1.1以下）"
categories: [ "记录" ]
tags: [ "kindle","越狱" ]
draft: false
slug: "LanguageBreak"
date: "2023-10-30 11:56:00"
---

10月29日，Github用户[notmarek][1]发布了一个仓库[LanguageBreak][2]，内容是版本号<=5.16.2.1.1的kindle设备的越狱（截至10月30日官网最新版本号为5.16.4），而在此之前仅5.14.2及以下的版本才有机会越狱。


<!--more-->

作者对此项目的介绍在：[https://www.mobileread.com/forums/showthread.php?t=356872][3]。该漏洞相关信息：[https://www.mobileread.com/forums/showthread.php?t=356766][4]，内容如下：

> Looking at the differences between 5.16.2 and 5.16.3, I think I found
> a smoking gun of a change.
> 
> There is small difference the versions of
> "usr/lib/blanket/langpicker.so.1.0" In the function:
> module_langpicker_utilDeleteExtraDictionaries
> 
> Before: Code:
>           __sprintf_chk(acStack_238,1,0x206,"rm -rf %s/%s","/mnt/us/documents/dictionaries",pcVar2);
>           pcVar6 = (char *)system(acStack_238); after: Code:
>           __sprintf_chk(acStack_830,1,0x200,"%s/%s","/mnt/us/documents/dictionaries",pcVar2);
>           pcVar6 = (char *)lab126_rmdir(acStack_830); This was passing strings to "system()" which is a glaring problem.
> 
> Expanding out a bit: Code:   __dirp =
> opendir("/mnt/us/documents/dictionaries");   if (__dirp == (DIR *)0x0)
> {
>     if (iVar1 == 0) {
>       return;
>     }   }   else { LAB_000134d4:
>     pdVar4 = readdir(__dirp);
>     if (pdVar4 != (dirent *)0x0) {
>       pcVar2 = pdVar4->d_name;
>       iVar5 = strcmp(pcVar2,".");
>       if (((iVar5 != 0) && (iVar5 = strcmp(pcVar2,".."), iVar5 != 0)) &&
>          (pcVar6 = strchr(pcVar2,0x2e), pcVar6 == (char *)0x0)) {
>         puVar7 = (undefined4 *)g_list_find_custom(iVar1,pcVar2,&LAB_00012534);
>         if (puVar7 == (undefined4 *)0x0) {
>           __sprintf_chk(acStack_238,1,0x206,"rm -rf %s/%s","/mnt/us/documents/dictionaries",pcVar2);
>           pcVar6 = (char *)system(acStack_238);
>           if ((pcVar6 == (char *)0x0) || ((g_blanket_llog_mask & 0x2000000) == 0))
>           goto LAB_000134d4;
>           pcVar2 = 
>           "E langpicker:DELETE_DICTIONARIES_FAILED:returnCode=%d:Error deleting unneeded dictionary  directory" It looks like this code walks
> through "/mnt/us/documents/dictionaries" (which is on the exposed file
> system!)
> 
> For each filename it finds, if it isn't in the list, it will append
> that filename to a string and pass it to system.
> 
> Using Shell escapes such as Code: `someprogram` or Code: $(some
> program) should be valid filenames. However, slashes are NOT normally
> allowed (is that why there was a mention of corrupting the VFAT file
> system?)
> 
> Looking at the calling function, it appears to be: 'changeLocale'
> 
> This is called from the startup script "etc/upstart/langpicker.conf"
> Code:
>         # send the event to langpicker module to install the language
>         lipc-send-event com.lab126.blanket.langpicker changeLocale -s "en-US" and from "langPicker" ("language_picker.js") Code:
>        setTimeout(function() {
>             nativeBridge.sendLipcEvent(LIPC_PILLOW_SOURCE, "changeLocale", changeLocaleParams);
>             actionsInactive = false;
>         }, SPLASH_EVENT_TIME_OUT); It does NOT appear to be called when changing the language in the GUI - but that uses Java (settings
> booklet) and not Javascript (language_picker.js).
> 
> So, the exploit path seems to be: Create a file in
> documents/dictionaries with Shell injection to run something Use
> 'mesquito' to call the LIPC function changeLocale

在原文下面有很多用户表示通过这个方法已经成功实现越狱了。
包括但不限于：
Kindle 第 10 代（5.14.3.2）
KPW4和KPW5 （5.16.2.1和5.16.2）
KPW2（5.12.2.2）
但也有很多人表示操作过程中遇到问题，因为项目是刚发布的，后续应该会有修复。


----------
越狱的相关说明（机翻）：

越狱部分：

> 1.在 Kindle 搜索栏中输入 ;enter_demo
> 2.重新启动设备
> 3.进入演示模式后，跳过 wifi 设置，输入随机值进行商店注册
> 4.跳过搜索演示有效载荷
> 5.选择 "标准 "演示类型
> 6.在提示符下按 "完成"，以侧载内容。
> 7.演示设置完成后，做 "秘密手势"（双指轻点屏幕右下角，然后向左滑动）
> 8.在搜索栏中输入 ;demo 进入演示配置菜单
> 9.选择 "侧载内容 "选项
> 10.将 LanguageBreak 文件夹的内容复制到 Kindle - 合并并替换所有文件
> 11.拔下 Kindle 插头，返回演示菜单
> 12.选择 "转售设备 "选项
> 13.然后按 "是/转售
> 14.现在等待按下电源按钮启动
> 15.一旦出现，将你的 kindle 插回电脑，再次将 LanguageBreak 文件夹的内容复制到电脑中，覆盖文件，然后安全弹出。
> 16.按照屏幕上的指示按住电源按钮
> 17.几秒钟后，你将进入语言选择菜单
> 18.选择 "中文"（在 "奇特的伪托语言 "上面的那个）。
> 19.你的 kindle 应该会重启，屏幕上会出现一些日志信息.

越狱后的操作：

> 1.设备重启后，在搜索栏中输入 ;uzb
> 2.将设备连接到 PC，然后将 update_hotfix_languagebreak.bin 复制到 Kindle 存储根目录下。
> 3.弹出设备，输入 ;dsts 或向下滑动并选择设置图标，进入设备设置菜单
> 4.选择 "更新你的 Kindle"，安装自定义修复程序
> 5.这将使您的设备退出演示模式，并清理不需要的越狱文件。


----------
过段时间我拿自己的KPW3试试（反正我有TTL线，随时跑路）

  [1]: https://github.com/notmarek
  [2]: https://github.com/notmarek/LanguageBreak
  [3]: https://www.mobileread.com/forums/showthread.php?t=356872
  [4]: https://www.mobileread.com/forums/showthread.php?t=356766