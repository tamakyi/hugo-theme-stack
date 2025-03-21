---
title: "记录在raspbian上折腾pikvm的失败过程"
categories: [ "记录" ]
tags: [ "pikvm","raspbian" ]
draft: false
slug: "pikvm_with_raspbian"
date: "2023-08-10 18:02:00"
---

###前言
 很久之前买过一款kvm套件，基于树莓派4b的拓展版，能够通过USB和跳线起到控制设备开关机以及远程操作的作用，但是[pikvm官方][1]只发布了基于32位archlinuxarm的固件，在其官网的QA处有这么一段描述：

> Why is PiKVM OS based on Arch Linux ARM and not Raspbian / Raspberry Pi OS? &amp;gt; There are several reasons:
> Several years ago, when PiKVM was just starting out, Raspbian didn‘t have a minimalistic image and the transition to systemd was in full swing, which is why the distribution was not too stable. Raspbian did not have all the necessary packages in the repositories to satisfy most software dependencies.
> PiKVM was born as a pet project, and the founder likes Arch the most.
> However, we plan to provide an alternative OS image based on Raspbian in the future - now it is quite stable. 

  大概意思就是pikvm起步早，当时raspbian还没成型，且raspbian的库中不能满足pikvm的依赖。**但是官方正在计划做基于raspbian的固件**，简而言之就是现在没有。 
  但对我来说，arch系的系统确实难用（**我是debian的狗**），另一方面我个人觉得，32位系统并不能发挥4B的全部实力。于是今天在逛pikvm的github的时候发现了这么一个issus：[Running pikvm on top of another OS][2]下有个评论：![github issus][3]
[第一个PDF][4]介绍了用树莓派官方镜像刷写工具写入32位的raspios并安装pikvm的过程，为什么一定要32位？我不明白。因此决定刷个64位的系统按他的步骤试试看。 
###刷个RaspiOS
系统方面我选择的是基于debian 11于20230503更新的当前最新系统。 ![raspbian system flash][5]
安装操作流程依照上面的PDF进行，需要注意的是其中提到的` enable &quot;Skip first-run wizard&quot; `选项当前已经不存在。 
###尝试安装pikvm
进入SSH，下载`https://kvmnerds.com/RPiKVM/install-pikvm-raspbian.sh`这个文件并赋予执行权限，然后直接`./install-pikvm-raspbian.sh`，开始一长串的wget（请注意:kvmnerds.com这个域名不能直连，需要自配梯子）
  ```shell
Running part 1 of PiKVM installer v5.6 script for Raspbian by @srepac


-> Getting Pi-KVM packages from https://kvmnerds.com/REPO/NEW

wget https://kvmnerds.com/REPO/NEW -O /var/cache/kvmd/packages.txt

wget https://kvmnerds.com/REPO/NEW/janus-0.11.3-raspbian-armv7l.pkg.tar.xz -O /var/cache/kvmd/janus-0.11.3-raspbian-armv7l.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-3.241-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-3.47-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmi-rpi2-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-platform-v0-hdmi-rpi2-3.241-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmi-rpi2-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-platform-v0-hdmi-rpi2-3.47-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmi-rpi3-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-platform-v0-hdmi-rpi3-3.241-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmi-rpi3-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-platform-v0-hdmi-rpi3-3.47-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmi-zero2w-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-platform-v0-hdmi-zero2w-3.241-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmi-zero2w-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-platform-v0-hdmi-zero2w-3.47-1-any.pkg.tar.xz
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmiusb-rpi2-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/k
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmiusb-rpi2-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kv
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmiusb-rpi3-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/k
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmiusb-rpi3-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kv
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmiusb-zero2w-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v0-hdmiusb-zero2w-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmi-rpi4-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmi-rpi4-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmi-zero2w-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kv
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmi-zero2w-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvm
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmi-zerow-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmi-zerow-3.54-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmiusb-rpi4-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/k
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v2-hdmiusb-rpi4-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kv
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v3-hdmi-rpi4-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v3-hdmi-rpi4-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v4mini-hdmi-rpi4-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v4mini-hdmi-rpi4-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/k
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v4plus-hdmi-rpi4-3.241-1-any.pkg.tar.xz -O /var/cache/kvmd/
wget https://kvmnerds.com/REPO/NEW/kvmd-platform-v4plus-hdmi-rpi4-3.47-1-any.pkg.tar.xz -O /var/cache/kvmd/k
wget https://kvmnerds.com/REPO/NEW/kvmd-webterm-0.42-1-any.pkg.tar.xz -O /var/cache/kvmd/kvmd-webterm-0.42-1

ls -l /var/cache/kvmd
total 3912
-rw-r--r-- 1 root root    4787 Aug 10 17:05 installer-20230810-17:04:49.log
-rw-r--r-- 1 root root 1858636 Feb 13  2022 janus-0.11.3-raspbian-armv7l.pkg.tar.xz
-rw-r--r-- 1 root root 1144228 Aug  7 08:16 kvmd-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root  749608 Feb 10  2022 kvmd-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6220 Aug  7 08:16 kvmd-platform-v0-hdmi-rpi2-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6220 Aug  7 08:16 kvmd-platform-v0-hdmi-rpi2-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6280 Aug  7 08:16 kvmd-platform-v0-hdmi-rpi3-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6280 Aug  7 08:16 kvmd-platform-v0-hdmi-rpi3-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6684 Aug  7 08:16 kvmd-platform-v0-hdmiusb-rpi2-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6684 Aug  7 08:16 kvmd-platform-v0-hdmiusb-rpi2-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6732 Aug  7 08:16 kvmd-platform-v0-hdmiusb-rpi3-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6732 Aug  7 08:16 kvmd-platform-v0-hdmiusb-rpi3-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6704 Aug  7 08:16 kvmd-platform-v0-hdmiusb-zero2w-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6704 Aug  7 08:16 kvmd-platform-v0-hdmiusb-zero2w-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6336 Aug  7 08:16 kvmd-platform-v0-hdmi-zero2w-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6336 Aug  7 08:16 kvmd-platform-v0-hdmi-zero2w-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6364 Aug  7 08:16 kvmd-platform-v2-hdmi-rpi4-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6364 Aug  7 08:16 kvmd-platform-v2-hdmi-rpi4-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6888 Aug  7 08:16 kvmd-platform-v2-hdmiusb-rpi4-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6888 Aug  7 08:16 kvmd-platform-v2-hdmiusb-rpi4-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6372 Aug  7 08:16 kvmd-platform-v2-hdmi-zero2w-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6372 Aug  7 08:16 kvmd-platform-v2-hdmi-zero2w-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    5776 Feb 19  2022 kvmd-platform-v2-hdmi-zerow-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    5776 Feb 19  2022 kvmd-platform-v2-hdmi-zerow-3.54-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6628 Aug  7 08:16 kvmd-platform-v3-hdmi-rpi4-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6628 Aug  7 08:16 kvmd-platform-v3-hdmi-rpi4-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6792 Aug  7 08:16 kvmd-platform-v4mini-hdmi-rpi4-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6792 Aug  7 08:16 kvmd-platform-v4mini-hdmi-rpi4-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6792 Aug  7 08:16 kvmd-platform-v4plus-hdmi-rpi4-3.241-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    6792 Aug  7 08:16 kvmd-platform-v4plus-hdmi-rpi4-3.47-1-any.pkg.tar.xz
-rw-r--r-- 1 root root    5940 Jun 26  2022 kvmd-webterm-0.42-1-any.pkg.tar.xz
-rw-r--r-- 1 root root   14716 Aug 10 17:04 packages.txt

Choose which capture device you will use:

  1 - USB dongle
  2 - v2 CSI
  3 - V3 HAT
```
我的平台是V3 HA3,因此选择3：
```shell
Platform selected -> kvmd-platform-v3-hdmi-rpi4

Please verify the platform selected above.

Press ENTER to continue or CTRL+C to break out of script.
``` 
回车确认，然后会进入安装依赖和编译过程流程（过程中会有`apt does not have a stable CLI interface`的提示，忽略即可）：
```shell
/boot/config.txt

# For more options and information see
# http://rpf.io/configtxt
# Some settings may impact device functionality. See link above for details

# uncomment if you get no picture on HDMI for a default "safe" mode
#hdmi_safe=1

# uncomment the following to adjust overscan. Use positive numbers if console
# goes off screen, and negative if there is too much border
#overscan_left=16
#overscan_right=16
#overscan_top=16
#overscan_bottom=16

# uncomment to force a console size. By default it will be display's size minus
# overscan.
#framebuffer_width=1280
#framebuffer_height=720

# uncomment if hdmi display is not detected and composite is being output
#hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
#hdmi_group=1
#hdmi_mode=1

# uncomment to force a HDMI mode rather than DVI. This can make audio work in
# DMT (computer monitor) modes
#hdmi_drive=2

# uncomment to increase signal to HDMI, if you have interference, blanking, or
# no display
#config_hdmi_boost=4

# uncomment for composite PAL
#sdtv_mode=2

#uncomment to overclock the arm. 700 MHz is the default.
#arm_freq=800

# Uncomment some or all of these to enable the optional hardware interfaces
#dtparam=i2c_arm=on
#dtparam=i2s=on
#dtparam=spi=on

# Uncomment this to enable infrared communication.
#dtoverlay=gpio-ir,gpio_pin=17
#dtoverlay=gpio-ir-tx,gpio_pin=18

# Additional overlays and parameters are documented /boot/overlays/README

# Enable audio (loads snd_bcm2835)
dtparam=audio=on

# Automatically load overlays for detected cameras
camera_auto_detect=1

# Automatically load overlays for detected DSI displays
display_auto_detect=1

# Enable DRM VC4 V3D driver
dtoverlay=vc4-kms-v3d
max_framebuffers=2

# Run in 64-bit mode
arm_64bit=1

# Disable compensation for displays with overscan
disable_overscan=1

[cm4]
# Enable host mode on the 2711 built-in XHCI USB controller.
# This line should be removed if the legacy DWC2 controller is required
# (e.g. for USB device mode) or if USB support is not required.
otg_mode=1

[all]

[pi4]
# Run as fast as firmware / board allows
arm_boost=1

[all]
# srepac custom configs

hdmi_force_hotplug=1
gpu_mem=128
enable_uart=1
dtoverlay=tc358743
dtoverlay=disable-bt
dtoverlay=dwc2,dr_mode=peripheral
dtparam=act_led_gpio=13

# HDMI audio capture
dtoverlay=tc358743-audio

# SPI (AUM)
dtoverlay=spi0-1cs

# I2C (display)
dtparam=i2c_arm=on

# Clock
dtoverlay=i2c-rtc,pcf8563

/etc/modules

# /etc/modules: kernel modules to load at boot time.
#
# This file contains the names of kernel modules that should be loaded
# at boot time, one per line. Lines beginning with "#" are ignored.

i2c-dev
tc358743
dwc2
libcomposite


/etc/udev/rules.d/99-kvmd.rules

# https://unix.stackexchange.com/questions/66901/how-to-bind-usb-device-under-a-static-name
# https://wiki.archlinux.org/index.php/Udev#Setting_static_device_names
KERNEL=="video[0-9]*", SUBSYSTEM=="video4linux", KERNELS=="fe801000.csi|fe801000.csi1", ATTR{name}=="unicam-
KERNEL=="hidg0", GROUP="kvmd", SYMLINK+="kvmd-hid-keyboard"
KERNEL=="hidg1", GROUP="kvmd", SYMLINK+="kvmd-hid-mouse"
KERNEL=="hidg2", GROUP="kvmd", SYMLINK+="kvmd-hid-mouse-alt"

-> Installing dependencies for pikvm
apt install -y nginx python3 net-tools bc expect v4l-utils iptables vim dos2unix screen tmate nfs-common gpi

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

apt install -y python3-pip

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

pip3 install dbus-next
apt-get install -y python3-aiofiles
apt-get install -y python3-aiohttp
apt-get install -y python3-appdirs
apt-get install -y python3-asn1crypto
apt-get install -y python3-async-timeout
apt-get install -y python3-bottle
apt-get install -y python3-cffi
apt-get install -y python3-chardet
apt-get install -y python3-click
apt-get install -y python3-colorama
apt-get install -y python3-cryptography
apt-get install -y python3-dateutil
apt-get install -y python3-dbus
apt-get install -y python3-hidapi
apt-get install -y python3-idna
apt-get install -y python3-libgpiod
apt-get install -y python3-marshmallow
apt-get install -y python3-more-itertools
apt-get install -y python3-multidict
apt-get install -y python3-netifaces
apt-get install -y python3-packaging
apt-get install -y python3-passlib
apt-get install -y python3-pillow
apt-get install -y python3-ply
apt-get install -y python3-psutil
apt-get install -y python3-pycparser
apt-get install -y python3-pyelftools
apt-get install -y python3-pyghmi
apt-get install -y python3-pygments
apt-get install -y python3-pyparsing
apt-get install -y python3-requests
apt-get install -y python3-semantic-version
apt-get install -y python3-serial
apt-get install -y python3-setproctitle
apt-get install -y python3-setuptools
apt-get install -y python3-six
apt-get install -y python3-spidev
apt-get install -y python3-systemd
apt-get install -y python3-tabulate
apt-get install -y python3-urllib3
apt-get install -y python3-wrapt
apt-get install -y python3-xlib
apt-get install -y python3-yaml
apt-get install -y python3-yarl
apt-get install -y python3-serial-asyncio
Selecting previously unselected package libev4:arm64.
(Reading database ... 100902 files and directories currently installed.)
Preparing to unpack libev4_4.33-1_arm64.deb ...
Unpacking libev4:arm64 (1:4.33-1) ...
Setting up libev4:arm64 (1:4.33-1) ...
Processing triggers for libc-bin (2.31-13+rpt2+rpi1+deb11u5) ...
(Reading database ... 100908 files and directories currently installed.)
Preparing to unpack libjson-c5_0.15-2_arm64.deb ...
Unpacking libjson-c5:arm64 (0.15-2) over (0.15-2) ...
Setting up libjson-c5:arm64 (0.15-2) ...
Processing triggers for libc-bin (2.31-13+rpt2+rpi1+deb11u5) ...
(Reading database ... 100908 files and directories currently installed.)
Preparing to unpack libuv1_1.40.0-2_arm64.deb ...
Unpacking libuv1:arm64 (1.40.0-2) over (1.40.0-2) ...
Setting up libuv1:arm64 (1.40.0-2) ...
Processing triggers for libc-bin (2.31-13+rpt2+rpi1+deb11u5) ...
Selecting previously unselected package ttyd.
(Reading database ... 100908 files and directories currently installed.)
Preparing to unpack ttyd_1.6.3-3~bpo11+1_arm64.deb ...
Unpacking ttyd (1.6.3-3~bpo11+1) ...
Setting up ttyd (1.6.3-3~bpo11+1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/ttyd.service → /lib/systemd/system/ttyd.service.
Processing triggers for man-db (2.9.4-2) ...

ttyd version: ttyd version 1.6.3


-> Building ustreamer

apt install -y build-essential libssl-dev libevent-dev libjpeg-dev libbsd-dev libraspberrypi-dev libgpiod-de

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Cloning into 'ustreamer'...
remote: Enumerating objects: 174, done.
remote: Counting objects: 100% (174/174), done.
remote: Compressing objects: 100% (107/107), done.
remote: Total 174 (delta 62), reused 136 (delta 54), pack-reused 0
Receiving objects: 100% (174/174), 172.77 KiB | 1.31 MiB/s, done.
Resolving deltas: 100% (62/62), done.
make apps
make[1]: Entering directory '/tmp/ustreamer'
make -C src
make[2]: Entering directory '/tmp/ustreamer/src'
-- CC libs/base64.c
-- CC libs/frame.c
-- CC libs/logging.c
-- CC libs/memsink.c
-- CC libs/options.c
-- CC libs/unjpeg.c
-- CC ustreamer/blank.c
-- CC ustreamer/data/blank_jpeg.c
-- CC ustreamer/data/favicon_ico.c
-- CC ustreamer/data/index_html.c
-- CC ustreamer/device.c
-- CC ustreamer/encoder.c
-- CC ustreamer/encoders/cpu/encoder.c
-- CC ustreamer/encoders/hw/encoder.c
-- CC ustreamer/h264.c
-- CC ustreamer/http/bev.c
-- CC ustreamer/http/mime.c
-- CC ustreamer/http/path.c
-- CC ustreamer/http/server.c
-- CC ustreamer/http/static.c
-- CC ustreamer/http/unix.c
-- CC ustreamer/http/uri.c
-- CC ustreamer/m2m.c
-- CC ustreamer/main.c
-- CC ustreamer/options.c
-- CC ustreamer/stream.c
-- CC ustreamer/workers.c
-- CC ustreamer/gpio/gpio.c
-- CC ustreamer/http/systemd/systemd.c
== LD ustreamer.bin
-- CC dump/file.c
-- CC dump/main.c
== LD ustreamer-dump.bin
make[2]: Leaving directory '/tmp/ustreamer/src'
make[1]: Leaving directory '/tmp/ustreamer'
make apps
make[1]: Entering directory '/tmp/ustreamer'
make -C src
make[2]: Entering directory '/tmp/ustreamer/src'
make[2]: Nothing to be done for 'all'.
make[2]: Leaving directory '/tmp/ustreamer/src'
make[1]: Leaving directory '/tmp/ustreamer'
make -C src install
make[1]: Entering directory '/tmp/ustreamer/src'
mkdir -p /usr/local/bin
install -m755 ustreamer.bin /usr/local/bin/ustreamer
install -m755 ustreamer-dump.bin /usr/local/bin/ustreamer-dump
make[1]: Leaving directory '/tmp/ustreamer/src'
mkdir -p /usr/local/share/man/man1
for man in ustreamer.1 ustreamer-dump.1; do \
	install -m644 man/$man /usr/local/share/man/man1/$man; \
	gzip -f /usr/local/share/man/man1/$man; \
done
ustreamer version/features: 5.41
+ WITH_GPIO
+ WITH_SYSTEMD
+ WITH_PTHREAD_NP
+ WITH_SETPROCTITLE
+ HAS_PDEATHSIG


-> Building wiringpi from source

Cloning into 'WiringPi'...
remote: Enumerating objects: 1733, done.
remote: Counting objects: 100% (615/615), done.
remote: Compressing objects: 100% (114/114), done.
remote: Total 1733 (delta 555), reused 502 (delta 501), pack-reused 1118
Receiving objects: 100% (1733/1733), 803.57 KiB | 1.24 MiB/s, done.
Resolving deltas: 100% (1188/1188), done.
wiringPi Build script
=====================


WiringPi Library
[UnInstall]
[Compile] wiringPi.c
[Compile] wiringSerial.c
[Compile] wiringShift.c
[Compile] piHiPri.c
[Compile] piThread.c
[Compile] wiringPiSPI.c
[Compile] wiringPiI2C.c
[Compile] softPwm.c
[Compile] softTone.c
[Compile] mcp23008.c
[Compile] mcp23016.c
[Compile] mcp23017.c
[Compile] mcp23s08.c
[Compile] mcp23s17.c
[Compile] sr595.c
[Compile] pcf8574.c
[Compile] pcf8591.c
[Compile] mcp3002.c
[Compile] mcp3004.c
[Compile] mcp4802.c
[Compile] mcp3422.c
[Compile] max31855.c
[Compile] max5322.c
[Compile] ads1115.c
[Compile] sn3218.c
[Compile] bmp180.c
[Compile] htu21d.c
[Compile] ds18b20.c
[Compile] rht03.c
[Compile] drcSerial.c
[Compile] drcNet.c
[Compile] pseudoPins.c
[Compile] wpiExtensions.c
pseudoPins.c: In function ‘myAnalogRead’:
pseudoPins.c:50:16: warning: cast to pointer from integer of different size [-Wint-to-pointer-cast]
   50 |   int *ptr   = (int *)node->data0 ;
      |                ^
pseudoPins.c: In function ‘myAnalogWrite’:
pseudoPins.c:59:16: warning: cast to pointer from integer of different size [-Wint-to-pointer-cast]
   59 |   int *ptr   = (int *)node->data0 ;
      |                ^
pseudoPins.c: In function ‘pseudoPinsSetup’:
pseudoPins.c:89:17: warning: cast from pointer to integer of different size [-Wpointer-to-int-cast]
   89 |   node->data0 = (unsigned int)ptr ;
      |                 ^
[Link (Dynamic)]
[Install Headers]
[Install Dynamic Lib]

WiringPi Devices Library
[UnInstall]
[Compile] ds1302.c
[Compile] maxdetect.c
[Compile] piNes.c
[Compile] gertboard.c
[Compile] piFace.c
[Compile] lcd128x64.c
[Compile] lcd.c
[Compile] scrollPhat.c
[Compile] piGlow.c
[Link (Dynamic)]
[Install Headers]
[Install Dynamic Lib]

GPIO Utility
[Compile] gpio.c
[Compile] readall.c
[Link]
[Install]

All Done.

NOTE: To compile programs with wiringPi, you need to add:
    -lwiringPi
  to your compile line(s) To use the Gertboard, MaxDetect, etc.
  code (the devLib), you need to also add:
    -lwiringPiDev
  to your compile line(s).

gpio version: 2.70
Copyright (c) 2012-2018 Gordon Henderson
This is free software with ABSOLUTELY NO WARRANTY.
For details type: gpio -warranty

Raspberry Pi Details:
  Type: Pi 4B, Revision: 01, Memory: 4096MB, Maker: Sony 
  * Device tree is enabled.
  *--> Raspberry Pi 4 Model B Rev 1.1
  * This Raspberry Pi supports user-level GPIO access.
-> Enabling kvmd-nginx kvmd-webterm kvmd-otg and kvmd services, but do not start them.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-nginx.service → /lib/systemd/system/kvmd-ng
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-webterm.service → /lib/systemd/system/kvmd-
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-otg.service → /lib/systemd/system/kvmd-otg.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd.service → /lib/systemd/system/kvmd.service.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-fix.service → /lib/systemd/system/kvmd-fix.
Created symlink /etc/systemd/system/multi-user.target.wants/kvmd-tc358743.service → /lib/systemd/system/kvmd


Reboot is required to create kvmd users and groups.
Please re-run this script after reboot to complete the install.

Press ENTER to continue or CTRL+C to break out of script.
```
最后这里提示`Reboot is required to create kvmd users and groups.Please re-run this script after reboot to complete the install.`，会在重启之后创建一个kvmd用户和组，要求重启后再次运行这个脚本，按下回车之后会自动重启。重启过后再次执行脚本进入第二阶段：
```shell
Running part 2 of PiKVM installer v5.6 script for Raspbian by @srepac
==> Ensuring KVMD users and groups ...

-> Disabling nginx service, so that we can use kvmd-nginx instead
Synchronizing state of nginx.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable nginx
Removed /etc/systemd/system/multi-user.target.wants/nginx.service.

-> Creating symlinks for use with kvmd python scripts

-> Creating kvmd-webterm homedir
drwxr-xr-x 2 kvmd-webterm root 4096 Aug 10 17:16 /home/kvmd-webterm
ConfigError: Invalid value '' for key 'kvmd/streamer/unix': None argument is not a valid absolute path
Did kvmd -m run properly?  [y/n] y

-> Restoring config files
-rw-r--r-- 1 root root 11360 Nov 20  2021 tools.js
Getting most current tools.js file... done
-rw-r--r-- 1 root root 11856 Aug 10 17:18 tools.js
-rw-r--r-- 1 root root 11360 Nov 20  2021 tools.js.20230810
Updating CSS files as per pikvm/pikvm#599: fixed webui windows oversizing

 /usr/share/kvmd/web/share/css/kvm/stream.css
 /usr/share/kvmd/web/share/css/login/login.css
 /usr/share/kvmd/web/share/css/modal.css
 /usr/share/kvmd/web/share/css/start.css
 /usr/share/kvmd/web/share/css/vars.css
 /usr/share/kvmd/web/share/css/window.css
Job for kvmd-otg.service failed because the control process exited with error code.
See "systemctl status kvmd-otg.service" and "journalctl -xe" for details.
Found existing installation: Pillow 8.1.2
Not uninstalling pillow at /usr/lib/python3/dist-packages, outside environment /usr
Can't uninstall 'Pillow'. No files were found to uninstall.

Check kvmd devices

lrwxrwxrwx 1 root root 6 Aug 10 17:18 /dev/kvmd-video -> video0

You should see devices for keyboard, mouse, and video.

Point a browser to https://tama
If it doesn't work, then reboot one last time.
Please make sure kvmd services are running after reboot.
Raspbian pikvm installer script completed on Thu 10 Aug 17:18:18 CST 2023
``` 
好像是受上面的配置影响，字面上理解是OTG这部分出问题了，但是不管了，先进`https://tama`看看 ： ![500报错][6] 直接报错了...查日志看看：
```
root@tama:~/pikvm# journalctl -fu kvmd
-- Journal begins at Wed 2023-05-03 11:23:19 CST. --
Aug 10 17:25:26 tama systemd[1]: kvmd.service: Main process exited, code=exited, status=1/FAILURE
Aug 10 17:25:27 tama kvmd-cleanup[3136]: ConfigError: Invalid value '' for key 'kvmd/streamer/unix': None ar
Aug 10 17:25:27 tama systemd[1]: kvmd.service: Control process exited, code=exited, status=1/FAILURE
Aug 10 17:25:27 tama systemd[1]: kvmd.service: Failed with result 'exit-code'.
Aug 10 17:25:27 tama systemd[1]: kvmd.service: Consumed 1.483s CPU time.
Aug 10 17:25:30 tama systemd[1]: kvmd.service: Scheduled restart job, restart counter is at 93.
Aug 10 17:25:30 tama systemd[1]: Stopped PiKVM - The main daemon.
Aug 10 17:25:30 tama systemd[1]: kvmd.service: Consumed 1.483s CPU time.
Aug 10 17:25:30 tama systemd[1]: Started PiKVM - The main daemon.
Aug 10 17:25:31 tama kvmd[3148]: ConfigError: Invalid value '' for key 'kvmd/streamer/unix': None argument i
Aug 10 17:25:31 tama systemd[1]: kvmd.service: Main process exited, code=exited, status=1/FAILURE
Aug 10 17:25:31 tama kvmd-cleanup[3150]: ConfigError: Invalid value '' for key 'kvmd/streamer/unix': None ar
Aug 10 17:25:32 tama systemd[1]: kvmd.service: Control process exited, code=exited, status=1/FAILURE
Aug 10 17:25:32 tama systemd[1]: kvmd.service: Failed with result 'exit-code'.
Aug 10 17:25:32 tama systemd[1]: kvmd.service: Consumed 1.490s CPU time.
qAug 10 17:25:35 tama systemd[1]: kvmd.service: Scheduled restart job, restart counter is at 94.
Aug 10 17:25:35 tama systemd[1]: Stopped PiKVM - The main daemon.
Aug 10 17:25:35 tama systemd[1]: kvmd.service: Consumed 1.490s CPU time.
 ```
 直接就卡在启动过程中反复重启了... 
如果再次运行脚本，会提示让你确认命令`kvmd -n`能否运行：
```shell
Running part 2 of PiKVM installer v5.6 script for Raspbian by @srepac
==> Ensuring KVMD users and groups ...

-> Disabling nginx service, so that we can use kvmd-nginx instead
Synchronizing state of nginx.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable nginx

-> Creating symlinks for use with kvmd python scripts

-> Creating kvmd-webterm homedir
drwxr-xr-x 2 kvmd-webterm root 4096 Aug 10 17:16 /home/kvmd-webterm
ConfigError: Invalid value '' for key 'kvmd/streamer/unix': None argument is not a valid absolute path
Did kvmd -m run properly?  [y/n] n
Please install missing packages as per the kvmd -m output in another ssh/terminal.
```
但是直接运行`kvmd -m`又会报同样的错：
```shell
root@tama:~/pikvm# kvmd -m
ConfigError: Invalid value '' for key 'kvmd/streamer/unix': None argument is not a valid absolute path
```
然后我看了眼脚本文件里面有这么一行:
```# 5.6   05/24/23 - added override entry to fix the Invalid value '' for key 'kvmd/server/unix': None argument error```
(|||ﾟДﾟ)?server报错是没有，但是还有个streamer报错啊？
我上discord翻了下，发现有人也报过这个错误：
![discord][7]
我试着把他这句加到我的配置文件的streamer下面：
```
    streamer:
        unix: /run/kvmd/kvmd.sock
        h264_bitrate:
            default: 5000
```
重启之后报错发生改变：
```
root@tama:/etc/kvmd# journalctl -fu kvmd
-- Journal begins at Wed 2023-05-03 11:23:19 CST. --
Aug 10 18:17:01 tama systemd[1]: kvmd.service: Scheduled restart job, restart counter is at 33.
Aug 10 18:17:01 tama systemd[1]: Stopped PiKVM - The main daemon.
Aug 10 18:17:01 tama systemd[1]: kvmd.service: Consumed 1.492s CPU time.
Aug 10 18:17:01 tama systemd[1]: Started PiKVM - The main daemon.
Aug 10 18:17:02 tama kvmd[6664]: ConfigError: Invalid value '' for key 'ipmi/kvmd/unix': None argument is not a valid absolute path
Aug 10 18:17:02 tama systemd[1]: kvmd.service: Main process exited, code=exited, status=1/FAILURE
Aug 10 18:17:02 tama kvmd-cleanup[6670]: ConfigError: Invalid value '' for key 'ipmi/kvmd/unix': None argument is not a valid absolute path
Aug 10 18:17:02 tama systemd[1]: kvmd.service: Control process exited, code=exited, status=1/FAILURE
Aug 10 18:17:02 tama systemd[1]: kvmd.service: Failed with result 'exit-code'.
Aug 10 18:17:02 tama systemd[1]: kvmd.service: Consumed 1.489s CPU time.
```
这个key...
要放到哪去？
我放弃了。


----------
###转战blikvm
换个思路，blikvm的文档里面也有一个版本能在64位raspbian上跑，至少看起来是成功了，该文档地址位于：[https://github.com/ThomasVon2021/blikvm/blob/master/dev-readme.md][8]
其使用的环境为：

 1. Raspberry Pi OS Lite
 2. Release date: 20230201
 3. System: 64-bit
 4. Kernel version: 5.15
 5. Debian version: 11 (bullseye)
和新版的除了内核高点之外其他也差不到哪去。所以在最新版本上跑试试。
原文档就不放出来了，真的无力吐槽...就放一些出问题的地方吧...
 1. `git clone https://libwebsockets.org/repo/libwebsockets`链接是失效的，应该使用`git clone https://github.com/warmcat/libwebsockets`
 2. php安装内容是偏向7.4的，但是`pecl install http://pecl.php.net/get/swoole-5.0.3.tgz`这个地方会报错表示该版本仅支持php8.0+，所以需要修改成4.8.10或者其他版本。还有就是`pecl install inotify`这个命令无法直接执行，需要去pecl下载包下来到本地手动安装。
 3. `Install kvmd services`这个地方更是吐血，首先是安装kvmd-main这个阶段，`kvmd-main.service`中`ExecStart`指向`/usr/bin/blikvm/kvmd-main`，但是这个文件并不在这个目录，导致安装会出错，同时这个`kvmd-main.service`依赖syslogd，需要手动运行`apt install busybox-syslogd`。其他的ustreamer以及kvmd-web均是如此，需要进所有文件去检查，路径有很多都是错的...
一通修改之后终于见到了熟悉的界面
```
----------------------------------------------------------
        Commercial IP KVM using Raspberry Pi 4
----------------------------------------------------------
Copyright (C) gzsgcs - All Rights Reserved
Unauthorized copying of any file in this project, via any medium is strictly prohibited
Proprietary and confidential
Written by gzsgcs<gzsgcs@gmail.com>, September 2022
----------------------------------------------------------
-------------------------
Version:2.11.2
Build:2023-08-08 15:13:24
-------------------------
[2023/08/10 23:36:26] [Log] Program start
[2023/08/10 23:36:26] [Log] Bootstrapping
[2023/08/10 23:36:26] [Log] Checking file system read write status
[2023/08/10 23:36:26] [Log] file system is readable/writable
[2023/08/10 23:36:26] [Log] Emulating network interfaces
[2023/08/10 23:36:26] [Log] Interface: [eth0] ,IP/Mask/Broadcast: [192.168.50.164/255.255.255.0/192.168.50.255]
[2023/08/10 23:36:26] [Log] You are running Release Version
[2023/08/10 23:36:26] [Warning] Custom configure file not found , skip!
[2023/08/10 23:36:26] [Log] admin config loaded!
[2023/08/10 23:36:26] [Log] check msd directory
[2023/08/10 23:36:26] [Log] Building static assets cache.
[2023/08/10 23:36:26] [Log] Static assets cache build success!
[2023/08/10 23:36:26] [Log] MSD.JSON not exists,initializing...
[2023/08/10 23:36:26] [Log] MSD json initialize success
[2023/08/10 23:36:26] [Log] Current Resolution is [640 x 480]
[2023/08/10 23:36:26] [Log] wait for ustreamer service
[2023/08/10 23:36:26] [Log] ustreamer service has started
[2023/08/10 23:36:26] [Log] wait for janus service
```
但是依然有好几个服务还没运行成功（janus 和 ustreamer)....再慢慢改吧。


----------
不改了，去死吧。

  [1]: https://pikvm.org/
  [2]: https://github.com/pikvm/pikvm/issues/374
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/08/10/64d4a19558178.png
  [4]: https://kvmnerds.com/RPiKVM/How_to_Install_PiKVM_onRaspbian_32-bit_OS.pdf
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/08/10/64d4a2b421570.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/08/10/64d4ac937ca7b.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/08/10/64d4b8cc9513b.png
  [8]: https://github.com/ThomasVon2021/blikvm/blob/master/dev-readme.md