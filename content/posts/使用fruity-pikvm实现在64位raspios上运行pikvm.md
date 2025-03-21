---
title: "使用fruity-pikvm实现在64位raspios上运行pikvm"
categories: [ "记录" ]
tags: [ "pikvm","树莓派" ]
draft: false
slug: "fruity-pikvm"
date: "2025-02-08 11:35:42"
---



我这个树莓派4买了好多年，基本上是吃灰的状态。因为自从我买了一套pikvm外设装上去之后，基本上就只能跑pikvm的那个魔改archlinux，我就是感觉各种别扭。一方面32位的arm系统并不能发挥PI4的性能，很多软件（包括一些容器）最低也需要aarch；另一方面，pikvm软件源在国内的访问速度简直就是一坨**，就算是挂了梯子都不一定能连的上，更不用说更新软件了。

今天偶然发现fruity-pikvm这个项目（https://github.com/jacobbar/fruity-pikvm），看介绍能够"将 PiKVM 安装移植到其他 SBC，如 OrangePi、BananaPi、MangoPi 等。且已在已在 OrangePi Zero 2 上进行了测试，目前，此脚本支持架构 **arm64/aarch64** 和 armhf/armv7l，并且应该适用于任何基于 Debian 的发行版，例如 Ubuntu、**Debian**、Armbian 等......"

哦？

我直接跑回家下了坨2024-11-19的raspios刷上去，配置完环境之后直接按照步骤执行fruity-pikvm安装:

```shell
tamakyi@akarinbot:~ $ sudo apt install -y git
tamakyi@akarinbot:~ $ git clone https://kkgithub.com/jacobbar/fruity-pikvm
tamakyi@akarinbot:~ $ cd fruity-pikvm
tamakyi@akarinbot:~/fruity-pikvm $ sudo ./install.sh
```

安装过程只需要按一下y，其他都是在编译、安装和测试过程。但是最后安装fruity-pikvm时出现了错误：

```shell
-->> installing fruity-pikvm
Selecting previously unselected package fruity-pikvm.
dpkg: regarding fruity-pikvm_0.2_arm64.deb containing fruity-pikvm, pre-dependency problem:
 fruity-pikvm pre-depends on iptables
  iptables is not installed.

dpkg: error processing archive fruity-pikvm_0.2_arm64.deb (--install):
 pre-dependency problem - not installing fruity-pikvm
Errors were encountered while processing:
 fruity-pikvm_0.2_arm64.deb
sudo: kvmd: command not found
reboot and enjoy
```

他依赖iptables，但是raspiso默认没有，那装一个完了再重新安装fruity-pikvm就是了：

```shell
tamakyi@akarinbot:~/fruity-pikvm $ wget https://github.com/jacobbar/fruity-pikvm/releases/download/debfile/fruity-pikvm_0.2_arm64.deb
tamakyi@akarinbot:~/fruity-pikvm $ sudo dpkg -i fruity-pikvm_0.2_arm64.deb
```

接着继续报错，但是整体是能安装了：

```shell
tamakyi@akarinbot:~/fruity-pikvm $ sudo dpkg -i fruity-pikvm_0.2_arm64.deb 
(Reading database ... 133479 files and directories currently installed.)
Preparing to unpack fruity-pikvm_0.2_arm64.deb ...
Unpacking fruity-pikvm (0.2) ...
Setting up fruity-pikvm (0.2) ...
==> Ensuring KVMD users and groups ...
Creating group 'kvmd' with GID 991.
Creating group 'kvmd-pst' with GID 990.
Creating group 'kvmd-ipmi' with GID 989.
Creating group 'kvmd-vnc' with GID 988.
Creating group 'kvmd-nginx' with GID 987.
Creating group 'kvmd-janus' with GID 986.
Creating group 'kvmd-certbot' with GID 985.
Creating user 'kvmd' (PiKVM - The main daemon) with UID 991 and GID 991.
Creating user 'kvmd-pst' (PiKVM - Persistent storage) with UID 990 and GID 990.
Creating user 'kvmd-ipmi' (PiKVM - IPMI to KVMD proxy) with UID 989 and GID 989.
Creating user 'kvmd-vnc' (PiKVM - VNC to KVMD/Streamer proxy) with UID 988 and GID 988.
Creating user 'kvmd-nginx' (PiKVM - HTTP entrypoint) with UID 987 and GID 987.
Creating user 'kvmd-janus' (PiKVM - Janus WebRTC Gateway) with UID 986 and GID 986.
Creating user 'kvmd-certbot' (PiKVM - Certbot-Renew for KVMD-Nginx) with UID 985 and GID 985.
==> Generating KVMD-Nginx certificate ...
+ mkdir -p /etc/kvmd/nginx/ssl
+ rm -f '/etc/kvmd/nginx/ssl/*'
+ cd /etc/kvmd/nginx/ssl
+ openssl ecparam -out server.key -name prime256v1 -genkey
+ openssl req -new -x509 -sha256 -nodes -key server.key -out server.crt -days 3650 -subj /C=RU/ST=Moscow/L=Moscow/O=PiKVM/OU=PiKVM/CN=localhost
+ chown root:kvmd-nginx /etc/kvmd/nginx/ssl/server.crt /etc/kvmd/nginx/ssl/server.key
+ chmod 440 /etc/kvmd/nginx/ssl/server.key
+ chmod 444 /etc/kvmd/nginx/ssl/server.crt
+ chmod 755 /etc/kvmd/nginx/ssl
==> Generating KVMD-VNC certificate ...
+ mkdir -p /etc/kvmd/vnc/ssl
+ rm -f '/etc/kvmd/vnc/ssl/*'
+ cd /etc/kvmd/vnc/ssl
+ openssl ecparam -out server.key -name prime256v1 -genkey
+ openssl req -new -x509 -sha256 -nodes -key server.key -out server.crt -days 3650 -subj /C=RU/ST=Moscow/L=Moscow/O=PiKVM/OU=PiKVM/CN=localhost
+ chown root:kvmd-vnc /etc/kvmd/vnc/ssl/server.crt /etc/kvmd/vnc/ssl/server.key
+ chmod 440 /etc/kvmd/vnc/ssl/server.key
+ chmod 444 /etc/kvmd/vnc/ssl/server.crt
+ chmod 755 /etc/kvmd/vnc/ssl
==> Disabling nginx service, so that we can use kvmd-nginx instead
Synchronizing state of nginx.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable nginx
Removed "/etc/systemd/system/multi-user.target.wants/nginx.service".
==> Disabling janus service
Synchronizing state of janus.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable janus
Removed "/etc/systemd/system/multi-user.target.wants/janus.service".
==> Ensuring KVMD-Webterm users and groups ...
Creating group 'kvmd-webterm' with GID 984.
Creating user 'kvmd-webterm' (Pi-KVM - Web terminal) with UID 984 and GID 984.
==> Making tesseract data link
==> Creating symlinks for use with kvmd python scripts
==> Adding path to python for KVMD
/var/lib/dpkg/info/fruity-pikvm.postinst: line 76: /usr/local/lib/python3.10/dist-packages/site-packages.pth: No such file or directory
==> Fixing armbian otg takeover
==> Enabling kvmd-nginx kvmd-webterm kvmd-otg and kvmd services, but do not start them.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-nginx.service → /lib/systemd/system/kvmd-nginx.service.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-webterm.service → /lib/systemd/system/kvmd-webterm.service.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-otg.service → /lib/systemd/system/kvmd-otg.service.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd.service → /lib/systemd/system/kvmd.service.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-fix.service → /lib/systemd/system/kvmd-fix.service.
```

这里报错的`/usr/local/lib/python3.10/dist-packages/site-packages.pth: No such file or directory`在项目的issus中有类似表述：（[Use a release with preinstalled python 3.10 for easier installation · Issue #5 · jacobbar/fruity-pikvm](https://github.com/jacobbar/fruity-pikvm/issues/5)），也就是python库默认没有添加kvmd-packages这个问题，检测是否真的出现可以使用`kvmd -m`命令测试，通常会提示`No module named 'kvmd'`。解决办法就是手动添加路径到默认库：

```shell
tamakyi@akarinbot:~ $ sudo nano /usr/local/lib/python3.10/site-packages/kvmd.pth
#写入内容：
/usr/local/lib/python3.10/kvmd-packages
#保存

tamakyi@akarinbot:~ $ sudo nano /usr/local/lib/python3.10/dist-packages/site-packages.pth
#写入内容：
/usr/local/lib/python3.10/kvmd-packages
#保存
```

`/usr/local/lib/python3.10/dist-packages/`这个路径可能不存在，可以手动创建。

完成之后重启PI，使用IP访问，发现提示500 ISE，检查kvmd-nginx进程发现丢失`kvmd.sock`

```shell
tamakyi@akarinbot:~ $ sudo systemctl status kvmd-nginx.service
...
connect() to unix:/run/kvmd/kvmd.sock failed (2: No such file or directory) while connecting to upstream, client: *.0.0.*, server: localhost, request: "GET / HTTP/2.0", subrequest: "/auth_check"
...
```

这个问题在上面的issus上也有提到，但是只说了python库默认没有添加kvmd-packages这个问题如何解决，500报错并没有解决。然后我继续检查日志：

```shell
tamakyi@akarinbot:~ $ sudo journalctl -u kvmd
...
kvmd[2180]: Traceback (most recent call last):
2月 07 14:53:39 akarinbot kvmd[2180]:   File "/usr/bin/kvmd", line 9, in <module>
2月 07 14:53:39 akarinbot kvmd[2180]:     main()
2月 07 14:53:39 akarinbot kvmd[2180]:   File "/usr/local/lib/python3.10/kvmd-packages/kvmd/apps/kvmd/__init__.py", line 75, in main
2月 07 14:53:39 akarinbot kvmd[2180]:     KvmdServer(
2月 07 14:53:39 akarinbot kvmd[2180]:   File "/usr/local/lib/python3.10/kvmd-packages/kvmd/apps/kvmd/server.py", line 263, in run
2月 07 14:53:39 akarinbot kvmd[2180]:     comp.sysprep()
2月 07 14:53:39 akarinbot kvmd[2180]:   File "/usr/local/lib/python3.10/kvmd-packages/kvmd/plugins/hid/otg/__init__.py", line 114, in sysprep
2月 07 14:53:39 akarinbot kvmd[2180]:     udc = usb.find_udc(self.__udc)
2月 07 14:53:39 akarinbot kvmd[2180]:   File "/usr/local/lib/python3.10/kvmd-packages/kvmd/usb.py", line 34, in find_udc
2月 07 14:53:39 akarinbot kvmd[2180]:     raise RuntimeError("Can't find any UDC")
2月 07 14:53:39 akarinbot kvmd[2180]: RuntimeError: Can't find any UDC
...
```

这里抛出一个`Can't find any UDC`的错误，UDC我查了下应该指的是`USB Device Controller`，检查是否有相关设备：`lsmod | grep usb`发现返回确实是空值。搜了下这个关键词，在[Fix KVMD not starting after updating piKVM - Technicus](https://techblog.jeppson.org/2022/03/fix-kvmd-not-starting-after-updating-pikvm/)这篇文章中发现有人提到在PI4上也出现了这个问题，然后我按照他在文章说的修改config.txt配置文件：

```shell
tamakyi@akarinbot:~ $ sudo nano /boot/firmware/config.txt
#添加以下内容到最后
dtoverlay=tc358743
dtoverlay=disable-bt
dtoverlay=dwc2,dr_mode=peripheral
#结束
```



然后再执行`lsmod | grep usb`，发现能检测到设备了。作者应该是对照了pikvm官方系统的配置文件和raspios的配置文件发现差异。

在这个issus下也有相关讨论：[RuntimeError：找不到任何 UDC ·问题 #1 ·原型/pikvm 容器](https://kkgithub.com/Prototyped/pikvm-container/issues/1)，这里的操作是：

```shell
tamakyi@akarinbot:~ $ sudo /usr/sbin/modprobe libcomposite
tamakyi@akarinbot:~ $ sudo nano /etc/modules-load.d/kvmd.conf
#添加以下内容
dwc2
libcomposite
#保存
```

这个方法选择不动`config.txt`配置文件，看上去也更安全，但是我用的是第一种方法，第二种并没有效果。

到这里的时候网页端已经能够使用admin/admin访问了，但是进入kvm页面发现无法显示，然后检查日志的时候发现疯狂报错：

```shell
tamakyi@akarinbot:~ $ sudo journalctl -u kvmd
...
kvmd.apps.kvmd.streamer          ERROR --- Unexpected streamer error: pid=2793
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/kvmd-packages/kvmd/apps/kvmd/streamer.py", line 428, in __streamer_task_loop
    raise RuntimeError("Streamer unexpectedly died")
RuntimeError: Streamer unexpectedly died
kvmd.apps.kvmd.info.system       ERROR --- Can't parse streamer features
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/kvmd-packages/kvmd/apps/kvmd/info/system.py", line 69, in __get_streamer_info
    (status, name) = map(str.strip, line.split(" "))
ValueError: not enough values to unpack (expected 2, got 1)
kvmd.apps.kvmd.streamer           INFO --- Started streamer pid=2835: /usr/bin/ustreamer --device=/dev/kvmd-video --persistent --format=mjpeg --resolution=1920x1080 --desired-fps=40 --drop-same-frames=30 --last-as-blank=0 --unix=/run/kvmd/ustreamer.sock --unix-rm --unix-mode=0660 --exit-on-parent-death --process-name-prefix=kvmd/streamer --notify-parent --no-log-colors --sink=kvmd::ustreamer::jpeg --sink-mode=0660
kvmd.apps.kvmd.streamer           INFO --- => /usr/bin/ustreamer: error while loading shared libraries: libjpeg.so.8: cannot open shared object file: No such file or directory
kvmd.apps.kvmd.streamer          ERROR --- Unexpected streamer error: pid=2835
...
```

他要`libjpeg.so.8`这个东西，但很明显我没有：

```shell
tamakyi@akarinbot:~ $ sudo find / -name libjpeg*
...
/usr/lib/aarch64-linux-gnu/libjpeg.so.62
/usr/lib/aarch64-linux-gnu/libjpegxr.so.1.2
/usr/lib/aarch64-linux-gnu/libjpeg.so.62.3.0
...
```

在https://github.com/jacobbar/fruity-pikvm/issues/5中，他简单粗暴的从5.4的镜像里面提取出libjpeg.so.8和libjpeg.so.8.2.2两个文件扔到系统的`/usr/lib/aarch64-linux-gnu`里。现在版本都飞到6.6.74了，肯定是没5.4，那只能自己编译个：

```shell
tamakyi@akarinbot:~ $  wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.290.tar.xz
tamakyi@akarinbot:~/kernel $ tar xvf linux-5.4.290.tar.xz
tamakyi@akarinbot:~/kernel $ cd linux-5.4.290
tamakyi@akarinbot:~/kernel $ cp /boot/config-$(uname -r) .config
tamakyi@akarinbot:~/kernel $ nano .config
#把 #CONFIG_TRUSTED_KEYS is not set 修改为 CONFIG_TRUSTED_KEYS=""
tamakyi@akarinbot:~/kernel/linux-5.4.290 $ make menuconfig #需要调整配置用
tamakyi@akarinbot:~/kernel $ make -j4 #四线程编译
```

如果怕跑到半掉了可以开个screen。

**但是！我发现另一台不记得是香蕉还是香橙的PI的docker内居然找到了这玩意，反正一样是aarch，直接狠狠替换。**

```shell
(base) tamakyi@akarinbot-arm64:~$ sudo find / -name libjpeg*
[sudo] tamakyi 的密码：
...
/var/lib/docker/overlay2/001ccded49667e443e6b0d924d49a33745a8cf2634056ada168e84a81aeae3ac/diff/usr/lib/libjpeg.so.8.2.2
/var/lib/docker/overlay2/001ccded49667e443e6b0d924d49a33745a8cf2634056ada168e84a81aeae3ac/diff/usr/lib/libjpeg.so.8
...
```

还是不行，再看一下日志：

```shell
tamakyi@akarinbot:~ $ sudo journalctl -u kvmd
...
2月 07 16:44:06 akarinbot kvmd[986]: RuntimeError: Streamer unexpectedly died
2月 07 16:44:07 akarinbot kvmd[986]: kvmd.apps.kvmd.streamer           INFO --- Started streamer pid=2914: /usr/bin/ustreamer --device=/dev/kvmd-video --persistent --format=mjpeg --resolution=1920x1080 --desired-fps=40 --drop-same-frames=30 --last-as-blank=0 --unix=/run/kvmd/ustreamer.sock --unix-rm --unix-mode=0660>
2月 07 16:44:07 akarinbot kvmd[986]: kvmd.apps.kvmd.streamer           INFO --- => /usr/bin/ustreamer: error while loading shared libraries: libc.musl-aarch64.so.1: cannot open shared object file: No such file or directory
2月 07 16:44:07 akarinbot kvmd[986]: kvmd.apps.kvmd.streamer          ERROR --- Unexpected streamer error: pid=2914
2月 07 16:44:07 akarinbot kvmd[986]: Traceback (most recent call last):
2月 07 16:44:07 akarinbot kvmd[986]:   File "/usr/local/lib/python3.10/kvmd-packages/kvmd/apps/kvmd/streamer.py", line 428, in __streamer_task_loop
2月 07 16:44:07 akarinbot kvmd[986]:     raise RuntimeError("Streamer unexpectedly died")
...
```

又缺个`libc.musl-aarch64.so.1`，去网上找了下，没有提到这个文件的信息，但是有`libc.musl-x86_64.so.1`，换汤不换药嘛，那就

```shell
tamakyi@akarinbot:~ $ sudo apt install musl musl-dev
tamakyi@akarinbot:~ $ sudo ln -s /usr/lib/aarch64-linux-musl/libc.so /lib/libc.musl-aarch64.so.1
tamakyi@akarinbot:~ $ sudo systemctl restart kvmd
```

现在登陆进去没异常了，但是提示`No Singal`，再看看日志文件：

```shell
tamakyi@akarinbot:~ $ sudo journalctl -u kvmd
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- Starting streamer ...
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- Started streamer pid=8263: /usr/bin/ustreamer --device=/dev/kvmd-video --persistent --format=mjpeg --resolution=1920x1080 --desired-fps=40 --drop-same-frames=30 --last->
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.203      main] -- Starting PiKVM uStreamer 5.37 ...
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.204      main] -- Using internal blank placeholder
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.204      main] -- Using JPEG-sink: kvmd::ustreamer::jpeg
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.204      main] -- Listening HTTP on UNIX socket '/run/kvmd/ustreamer.sock'
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.204    stream] -- Using V4L2 device: /dev/kvmd-video
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.204    stream] -- Using desired FPS: 40
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => ================================================================================
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- ERROR [1037.205    stream] -- Can't access device: No such file or directory
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.205    stream] -- Waiting for the device access ...
2月 07 17:13:30 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1037.206      http] -- Starting HTTP eventloop ...
2月 07 17:13:31 akarinbot kvmd[7433]: aiohttp.access                    INFO --- [admin (token) / -] 'GET /auth/check HTTP/1.0' => 200; size=194 --- referer='https://*.*.*.*/kvm/'; user_agent='Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537>
2月 07 17:13:31 akarinbot kvmd[7433]: kvmd.apps.kvmd.streamer           INFO --- => -- INFO  [1038.367      http] -- HTTP: NEW client (now=1): [100.74.203.35]:0, id=bad9da595991fbbf
```

基本上正常了，但还有一个`Can't access device: No such file or directory`，总之先保证能跑起来了，真是心累。