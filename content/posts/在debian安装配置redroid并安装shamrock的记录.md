---
title: "在debian安装配置redroid并安装shamrock的记录"
categories: [ "记录" ]
tags: [ "debian","docker","redroid","android","ashmem-linux","binder_linux","shamrock","qq" ]
draft: false
slug: "redroit"
date: "2024-01-24 12:01:00"
---

### 前言

由于某讯对第三方QQ机器人的大力追杀，原有的机器人框架（miraiqq和go-cqhttp）等基本上都无法使用了，登录会直接代码45封号。在此之后发展出shamrock等项目，此类项目的特点是需要虚拟机或实机进行操作，意味着至少需要一个桌面环境或者一台闲置手机，开销相比之前1c512m都能跑的时候高太多。在网上冲浪的时候发现了可以将安卓的Dalvik/ART移植到Linux桌面环境，借此运行安卓APK程序，因此需要用到redroid这个软件。

redroid即Remote anDroid，是一个架设云手机的方案，可以透过docker在linux系统上跑安卓系统容器。相比于虚拟机方案，其优势在于：

- 开源

- 支持GPU加速+arm转X86转译器

- 与宿主机公用内核等相关资源

- 支持使用scrcpy进行连接

  ------

  

### 给内核添加相关模块

首先需要查询系统内核是否支持`binder`和`ashmem`两个功能，此功能为使用redroid必须的。查询可以通过执行：

```shell
lsmod | grep -e ashmem_linux -e binder_linux
```

进行查询，如果有输出可以略过此步骤。如果没有任何输出则表示内核没有配置好这两个模块，需要重新编译内核。对于Ubuntu系统，官方提供了[比较简单的流程](https://github.com/remote-android/redroit-doc)，对于其他系统则需要手工维护。

首先去下载当前使用内核版本的源码：

```shell
apt install linux-source-$(uname-r)
```

下载完的源码位于`/usr/src`，格式是`.xz`，进去之后解压源码包并进入解压的文件夹，然后执行

```shell
make menuconfig
```

在弹出界面的勾选：

```shell
Device Drivers--Android--Android Drivers--Binder Ipc Driver。 #binder
Device Drivers--Android--Android Drivers--Binder filesystem。 #binder
Device Drivers--Android--Staging Drivers--Android--Enable the Anonumous Shared Memory Subsystem #ashmem
General Setup--CPU/Task time and stats accounting--Pressure stall infomation tracking
```

后按`save`保存。

或者进去之后直接选择`exit`并选择`save config`。目录下会生成一个`.config`的文件，编辑该文件在最后添加以下内容:

```shell
CONFIG_ASHMEM=y
CONFIG_ANDROID=y
CONFIG_ANDROID_BINDER_IPC=y
CONFIG_ANDROID_BINDERFS=y
CONFIG_ANDROID_BINDER_DEVICES="binder,hwbinder,vndbinder,binderfs"
CONFIG_ANDROID_BINDER_IPC_SELFTEST=y
```

保存后执行`make` ，建议不要加j让他慢慢编译，经尝试这样会导致报错。

**注意**：可能会报错：`CONFIG_DEBUG_INFO_BTF`

这种情况就去`.config`文件里面添加以下内容：

```shell
CONFIG_DEBUG_INFO_DWARF5=n
CONFIG_DEBUG_INFO_BTF=n #原值为y
```



跑完后在目录下执行`modules_install`和`make install`，执行完成后检查内核是否安装到grub了：

```
(base) root@DevBot:/boot# cat /boot/grub/grub.cfg | grep menuentry
if [ x"${feature_menuentry_id}" = xy ]; then
  menuentry_id_option="--id"
  menuentry_id_option=""
export menuentry_id_option
menuentry 'Debian GNU/Linux' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
submenu 'Advanced options for Debian GNU/Linux' $menuentry_id_option 'gnulinux-advanced-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.5.10android' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.5.10android-advanced-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.5.10android (recovery mode)' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.5.10android-recovery-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.5.0-0.deb12.4-amd64' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.5.0-0.deb12.4-amd64-advanced-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.5.0-0.deb12.4-amd64 (recovery mode)' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.5.0-0.deb12.4-amd64-recovery-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.1.0-17-amd64' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.1.0-17-amd64-advanced-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.1.0-17-amd64 (recovery mode)' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.1.0-17-amd64-recovery-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.1.0-15-amd64' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.1.0-15-amd64-advanced-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
        menuentry 'Debian GNU/Linux, with Linux 6.1.0-15-amd64 (recovery mode)' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-6.1.0-15-amd64-recovery-9aba24c5-ec53-4108-b461-4bcc67d1d0dc' {
menuentry 'UEFI Firmware Settings' $menuentry_id_option 'uefi-firmware' {
```

可以看到`Debian GNU/Linux, with Linux 6.5.10android`已经配置进去了。

然后我们需要修改内核启动顺序：

```
#nano /etc/default/grub
GRUB_DEFAULT="1 >0"
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
GRUB_DISABLE_OS_PROBER=false
```

GRUB_DEFAULT为按menuentry顺序排序设置默认启动项，第一项为0，我这里对应的是`Debian GNU/Linux`，`Debian GNU/Linux, with Linux 6.5.10android`是子菜单`Advanced options for Debian GNU/Linux`下的第一项，所以这里应该设置为1 >0.注意"1"和">"之间有一个空格。

然后更新保存grub：

```
update-grub
```

查看是否配置正确：

```
cat /proc/cmdline
```

重启。然后直接一个`uname -a`

```shell
(base) tamakyi@DevBot:~$ uname -a
Linux AkarinDevBot 6.5.10android #2 SMP PREEMPT_DYNAMIC Wed Jan 24 00:43:00 HKT 2024 x86_64 GNU/Linux
```

如果还是不行（提示缺少ashmem）的话，去克隆这个仓库并执行安装加载：

```shell
git clone https://github.com/choff/anbox-modules
cd anbox-modules
sh ./INSTALL.sh
```

再检查是否启用完成：

```shell
(base) tamakyi@DevBot:~$ grep binder /proc/filesystems 
nodev   binder
(base) tamakyi@DevBot:~$ grep ashmem /proc/misc 
124 ashmem
```

有输出就表示正常启用模块了。如果如果还是用不了，那可能是你需要手动挂载binderfs：

```shell
mkdir /dev/binderfs
mount -t binder none /dev/binderfs/
```

然后重启再验证，至此redroid的内核依赖模块安装完成。

------

### 安装redroid

安装过docker的直接运行:

```shell
(base) root@DevBot:~# docker run -itd --rm --privileged \
    --pull always \
    -v ~/data:/data \
    -p 35555:5555 \
    redroid/redroid:13.0.0-latest
```

其中官方支持的版本如下：（截止20240124）

- Android 14 (redroid/redroid:14.0.0-latest)
- Android 14 64bit only (redroid/redroid:14.0.0_64only-latest)
- Android 13 (redroid/redroid:13.0.0-latest)
- Android 13 64bit only (redroid/redroid:13.0.0_64only-latest)
- Android 12 (redroid/redroid:12.0.0-latest)
- Android 12 64bit only (redroid/redroid:12.0.0_64only-latest)
- Android 11 (redroid/redroid:11.0.0-latest)
- Android 10 (redroid/redroid:10.0.0-latest)
- Android 9 (redroid/redroid:9.0.0-latest)
- Android 8.1 (redroid/redroid:8.1.0-latest)

版本并不是越高越好，越高占用内存越多，且限制更多，此处我选择安卓10，跑个shamrock足矣。
**经测试在安卓10内部打开设置会导致宿主机死机，并且重启后docker容器会消失，慎用**

检查是否正常使用：

```shell
# docker ps
CONTAINER ID   IMAGE                           COMMAND                   CREATED         STATUS         PORTS                                         NAMES
d51457fc4561   redroid/redroid:10.0.0-latest   "/init androidboot.h…"   3 minutes ago   Up 3 minutes   0.0.0.0:35555->5555/tcp, :::35555->5555/tcp   sweet_vaughan
```

下载adb套件：

```shell
wget https://dl.google.com/android/repository/platform-tools_r34.0.5-linux.zip #不一定是这个版本，具体以官网为准
unzip platform-tools_r34.0.5-linux.zip
cp platform-tools_r34.0.5-linux/adb /usr/bin
chmod +x /usr/bin/adb
```

连接进redroid：

```shell
(base) root@DevBot:~/platform-tools# adb connect localhost:35555 #前面把docker的5555端口映射到宿主的35555端口
connected to localhost:35555 
(base) root@DevBot:~/platform-tools# adb devices
List of devices attached
localhost:35555 device
(base) root@DevBot:~/platform-tools# adb -s localhost:35555 shell #实际上我这里有多个设备连接 所以需要指定设备
redroid_x86_64:/ $ 
redroid_x86_64:/ $ free -h
                total        used        free      shared     buffers
Mem:              14G        5.3G        8.2G         12M         82M
-/+ buffers/cache:           5.2G        8.2G
Swap:            976M           0        976M
redroid_x86_64:/ $ df -h
Filesystem     Size  Used Avail Use% Mounted on
overlay        218G   65G  141G  32% /
tmpfs           64M  592K   63M   1% /dev
shm             64M     0   64M   0% /dev/shm
/dev/sdb2      218G   65G  141G  32% /data
tmpfs          6.7G     0  6.7G   0% /mnt
tmpfs          6.7G     0  6.7G   0% /apex
/dev/fuse      218G   65G  141G  32% /storage/emulated
redroid_x86_64:/ $ uname -a
Linux localhost 6.5.10android #2 SMP PREEMPT_DYNAMIC Wed Jan 24 00:43:00 HKT 2024 x86_64
redroid_x86_64:/ $ 
```

查询下`build.prop`：

```shell
1|redroid_x86_64:/ $ su
1|redroid_x86_64:/ # cat /system/build.prop                     

# begin common build properties
# autogenerated by build/make/tools/buildinfo_common.sh
ro.system.build.date=Mon Oct 16 08:28:31 UTC 2023
ro.system.build.date.utc=1697444911
ro.system.build.fingerprint=redroid/redroid_x86_64/redroid_x86_64:10/QP1A.190711.019/frank10160828:userdebug/test-keys
ro.system.build.id=QP1A.190711.019
ro.system.build.tags=test-keys
ro.system.build.type=userdebug
ro.system.build.version.incremental=eng.frank.20231016.082901
ro.system.build.version.release=10
ro.system.build.version.sdk=29
ro.product.system.brand=redroid
ro.product.system.device=redroid_x86_64
ro.product.system.manufacturer=redroid
ro.product.system.model=redroid10_x86_64
ro.product.system.name=redroid_x86_64
# end common build properties
# begin build properties
# autogenerated by buildinfo.sh
ro.build.id=QP1A.190711.019
ro.build.display.id=redroid_x86_64-userdebug 10 QP1A.190711.019 eng.frank.20231016.082901 test-keys
ro.build.version.incremental=eng.frank.20231016.082901
ro.build.version.sdk=29
ro.build.version.preview_sdk=0
ro.build.version.preview_sdk_fingerprint=REL
ro.build.version.codename=REL
ro.build.version.all_codenames=REL
ro.build.version.release=10
ro.build.version.security_patch=2019-09-05
ro.build.version.base_os=
ro.build.version.min_supported_target_sdk=23
ro.build.date=Mon Oct 16 08:28:31 UTC 2023
ro.build.date.utc=1697444911
ro.build.type=userdebug
ro.build.user=frank
ro.build.host=redroid-builder
ro.build.tags=test-keys
ro.build.flavor=redroid_x86_64-userdebug
ro.build.ab_update=true
# ro.product.cpu.abi and ro.product.cpu.abi2 are obsolete,
# use ro.product.cpu.abilist instead.
ro.product.cpu.abi=x86_64
ro.product.cpu.abilist=x86_64,x86
ro.product.cpu.abilist32=x86
ro.product.cpu.abilist64=x86_64
ro.product.locale=en-US
ro.wifi.channels=
# ro.build.product is obsolete; use ro.product.device
ro.build.product=redroid_x86_64
# Do not try to parse description or thumbprint
ro.build.description=redroid_x86_64-userdebug 10 QP1A.190711.019 eng.frank.20231016.082901 test-keys
# end build properties

#
# from build/make/target/board/gsi_system.prop
#
# GSI always generate dex pre-opt in system image
ro.cp_system_other_odex=0

# GSI always disables adb authentication
ro.adb.secure=0

# GSI disables non-AOSP nnapi extensions on product partition
ro.nnapi.extensions.deny_on_product=true

# TODO(b/120679683): disable RescueParty before all problem apps solved
persist.sys.disable_rescue=true

# TODO(b/78105955): disable privapp_permissions checking before the bug solved
ro.control_privapp_permissions=disable

# TODO(b/136212765): the default for LMK
ro.lmk.kill_heaviest_task=true
ro.lmk.kill_timeout_ms=100
ro.lmk.use_minfree_levels=true
# end of build/make/target/board/gsi_system.prop

#
# ADDITIONAL_BUILD_PROPERTIES
#
ro.bionic.ld.warning=1
ro.treble.enabled=true
persist.debug.dalvik.vm.core_platform_api_policy=just-warn
dalvik.vm.lockprof.threshold=500
net.bt.name=Android
```

说明已经正常安装redroid并使用了。

------



### 配置安装shamrock


首先要把相关的东西准备好（此步骤我在windows远程操作了）：
下载:
`https://github.com/Genymobile/scrcpy/releases/download/v2.3.1/scrcpy-win64-v2.3.1.zip`
`https://downv6.qq.com/qqweb/QQ_1/android_apk/Android_9.0.15_64.apk`
`https://github.com/LSPosed/LSPatch/releases/download/v0.6/manager-v0.6-398-release.apk`
`https://github.com/RikkaApps/Shizuku/releases/download/v13.5.3/shizuku-v13.5.3.r1036.fff3f87-release.apk`
以及Shamrock安装包，因为使用的是Action版本，需要登陆Github下载，此处不放链接
，解压`scrcpy-win64-v2.3.1.zip`并把QQ、LSPatch、shizuku、Shamrock放在同一目录，打开powershell，执行：
```shell
./adb.exe connect 100.64.0.16:35555 #100.64.0.16为我的debian宿主机远程访问IP。
./adb.exe devices
List of devices attached
100.64.0.16:35555     device
./adb push Android_9.0.15_64.apk /sdcard/
./adb install LSPatch.apk
./adb install shizuku-v13.5.3.r1040.1fc3a2d-release.apk
./adb install Shamrock-v1.0.7.r260.64b7b00-all.apk
```
然后使用scrcpy：
```
scrcpy
```
会进到安卓界面，先打开shizuku，会提示要启用，我们回退到adb窗口，执行：
```
./adb shell sh /storage/emulated/0/Android/data/moe.shizuku.privileged.api/start.sh
```
然后shizuku提示`shizuku is running`。然后把`Authorized 0 applications`点开，勾选LSPatch，再打开LSPatch，到Manager--Apps点击加号，选择New Patch-Select apk(s) from storage 找到QQ安装包，点击start patch
![shizuku][1]
![lsp-patch][2]
![start patch][3]
等待打补丁完成，然后点击Install。
![patch done][4]
安装完成后点击LSPatch里的QQ，找到module scope，把Shamrock打勾，即允许Shamrock对QQ生效。
然后启动QQ，QQ就闪退了...
因为LSPatch目前已停止更新，后续是否会支持新版QQ尚未能明确，此文暂停更新。




------

### 参考文章

[QQ-bot 框架部署记录](https://dwcarrot.github.io/blog/2023/12/29/Notes-QQ-Bot-Framework)

[一次云服务器内核升级之旅 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/518415793)


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/24/65b0d331552e6.png
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/24/65b0d3ce0b407.png
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/24/65b0d3edd100b.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/01/24/65b0d43075df2.png