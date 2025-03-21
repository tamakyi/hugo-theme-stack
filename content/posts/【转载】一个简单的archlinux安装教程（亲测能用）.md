---
title: "【转载】一个简单的archlinux安装教程（亲测能用）"
categories: [ "记录" ]
tags: [ "archlinux" ]
draft: false
slug: "archlinux_install_guide"
date: "2023-08-16 22:35:00"
---

来自：[https://www.mivm.cn/archlinux-installation-guide-simple][1]


----------
###硬盘分区
如果你需要 Linux + Windows 双系统，建议使用两个物理硬盘安装，以下分区方式不适用于单硬盘双系统。如果你想要单硬盘双系统，可以加入 QQ 群向我询问。

执行`fdisk -l`查看硬盘以及分区列表，确定你要用于安装系统的硬盘路径，比如这里我用的是`/dev/sda`。loop 设备是光驱等虚拟设备，不是真实的硬盘，不能用于安装系统。

确认硬盘

如果你有多块硬盘，第一块硬盘一般为`/dev/sda` 或`/dev/nvme0n1` (NVME)，第二块硬盘一般为`/dev/sdb` 或`/dev/nvme0n2` (NVME)，以此类推。U 盘或移动硬盘也会被列出，注意不要搞错。

如果你的硬盘没有被列出，可能是在 BIOS 开启了 RAID 模式（部分笔记本会默认开启），前往 BIOS 将硬盘模式改为 AHCI 模式即可，更改后可能会导致现有的 Windows 无法启动。

下述分区操作将清空目标硬盘数据，请在操作前做好数据备份。

务必将下列命令中的${diskPath}替换为你的硬盘路径。示例：`sgdisk /dev/sda`

擦除分区表：`sgdisk -Z ${diskPath}`

创建大小为 260M 的 ESP 分区 （UEFI 引导必要分区）：`sgdisk -n=0:0:+260M -t=0:ef00 ${diskPath}`

使用剩余空间创建系统分区：`sgdisk -N=0 ${diskPath}`

显示分区信息：`fdisk -l ${diskPath}`，对比你的分区信息是否与下列信息相似，重点是分区类型 (Type)。
```
Disk ${diskPath}: *** GiB, *** bytes, *** sectors
Disk model: ***
Units: sectors of 1 * *** = *** bytes
Sector size (logical/physical): *** bytes / *** bytes
I/O size (minimum/optimal): ** bytes / *** bytes
Disklabel type: gpt
Disk identifier: ***

Device         Start    End    Sectors    Size    Type
${diskPath}1   ***      ***    ***        260M    EFI System
${diskPath}2   ***      ***    ***        ***G    Linux filesystem
```
PS：给 ESP 分区分配 260M 空间是为了兼容不同硬盘，不同的 UEFI 固件，以及存放系统内核和 initramfs。对于某些早期或者有缺陷的 UEFI 实现，可能至少需要分配 512M。

某些教程的分区步骤会创建一个额外的 SWAP（交换空间）分区，这里并不推荐创建，具体原因参考下面的【设置交换空间】章节

如果你的硬盘空间较大，或者想把系统和数据分别存放在不同的分区，想要多个分区也可以。

格式化并挂载分区
格式化 ESP 分区为 FAT32：`mkfs.fat -F 32 ${part1Path}`，把${part1Path}替换为第一个分区的路径，示例：`mkfs.fat -F 32 /dev/sda1`

注意：如果你在格式化 ESP 分区的时候提示：`WARNING: Not enough clusters for a 32 bit FAT!`，请尝试使用`mkfs.fat -s 2 -F 32 ${part1Path}`或`mkfs.fat -s 1 -F 32 ${part1Path}`命令来格式化 ESP 分区，否则 UEFI 固件可能会无法读取 ESP 分区。

格式化系统分区为 EXT4：`mkfs.ext4 -m 5 ${part2Path}`，把${part2Path}替换为第二个分区路径，示例：`mkfs.ext4 -m 5 /dev/sda2`

PS：-m是指定保留块所占百分比，默认值为 5，用作保留快的空间不能被使用。你可以减小这个值增加分区的可用空间，或者增大这个值提升固态硬盘的性能。

如果你想要更好的性能和更现代的功能，可以在系统分区尝试使用 XFS 文件系统：`mkfs.xfs ${part2Path}`

XFS 可能需要手动维护，参考：https://wiki.archlinux.org/title/XFS

PS：XFS 分区大小目前只支持扩容，不支持缩小。

当然你也可以在系统分区使用其他文件系统，比如 Btrfs、JFS、F2FS 等，你可以前往 ArchWiki 查看这些文件系统的对应文档。

挂载系统分区：`mount ${part2Path} /mnt`，把${part2Path}替换为第二个分区路径，示例：`mount /dev/sda2 /mnt`

创建 ESP 分区挂载路径：`mkdir /mnt/boot`

挂载 ESP 分区：`mount -o umask=0077 ${part1Path} /mnt/boot`，把${part1Path}替换为第一个分区路径，示例：`mount -o umask=0077 /dev/sda1 /mnt/boot`

检查分区是否挂载成功：df，如果输出包含类似以下行，则代表挂载成功。
```
Filesystem    Size    Used    Avail    Use%    Mounted on
${part2Path}  ***     ***     ***      ***%    /mnt
${part1Path}  ***     ***     ***      ***%    /mnt/boot
```
###设置中国镜像源（推荐）
安装环境默认启用了所有的软件镜像源，为了使安装更顺利，建议只使用位于中国的镜像，如果你不在中国或者对自己的网络有信心可以忽略此操作。

下载中国镜像列表：`curl -L -o /etc/pacman.d/mirrorlist "https://archlinux.org/mirrorlist/?country=CN"`

执行`nano /etc/pacman.d/mirrorlist`编辑镜像列表文件，删除`#Server = `前面的`#`以启用镜像，不需要全部启用，一般 3 个就够了。某些镜像同时支持 HTTP 和 HTTPS 协议，只需要删除 HTTPS 协议行的#。

PS：镜像列表默认按照字母排序，推荐安装完系统后选择一个速度较快的镜像，并把它放在其他镜像前面。阿里云、华为云、腾讯云 等云服务商也有 ArchLinux 软件源镜像，但是还没有加入官方镜像列表，大家可以手动添加使用。

PPS：不同的镜像更新间隔有些许不同，而 Arch 又是一个以滚动发布著称的发行版，软件更新频率较快。如果你对更新频率有较高的要求，建议选择更新间隔较短的镜像。

###安装系统
设置本地化环境：`export LANG=C`

安装所需软件包到系统分区：`pacstrap /mnt base linux linux-firmware bash-completion e2fsprogs nano networkmanager pacman-contrib sudo amd-ucode intel-ucode sof-firmware`

软件包介绍
base: ArchLinux 运行所需的基础软件包集合

linux: Linux 内核

linux-firmware: Linux 设备驱动集合，包含了绝大多数设备的驱动（固件）。

bash-completion: Bash 补全支持

e2fsprogs: EXT4 文件系统实用工具

nano: 文本编辑器

networkmanager: 网络管理器

pacman-contrib: pacman 相关实用脚本

sudo: 用于普通用户获取 root 权限

amd-ucode intel-ucode: AMD 和 Intel 的 CPU 微码更新，用于修补 CPU 漏洞。

sof-firmware: Open Sound 固件

虚拟机用户不需要安装amd-ucode intel-ucode，物理机用户也可以只安装适用于你电脑的 CPU 的微码包，比如 AMD CPU 用户就不需要安装intel-ucode。

如果你使用了 XFS 文件系统，将e2fsprogs替换为xfsprogs，或者替换为你的文件系统的工具包。

如果你的设备不是笔记本可以不安装sof-firmware，也可以等到稍后设备没声音时再安装它尝试修复。

可以趁安装的这段时间喝口水，它并不需要很久。

成功安装完成后执行`genfstab -U /mnt > /mnt/etc/fstab`生成 fstab 文件

最后执行`arch-chroot /mnt`进入新系统环境

###系统初始化设置
####设置时区为中国：`ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`，你也可以设置为所在的时区，完整的时区列表：https://twiki.org/cgi-bin/xtra/tzdatepick.html

####同步硬件时钟：`hwclock --systohc`

务必将下列命令中的${hostname}替换为你的主机名（例如：kanto）。主机名建议使用小写字母组成，最多 64 个字符，你可以使用下面的输入框验证主机名是否符合规则。

请输入主机名
####设置系统主机名：
`echo ${hostname} > /etc/hostname`

执行nano /etc/hosts编辑 hosts 文件并添加以下行：
```
127.0.0.1 localhost
127.0.0.1 ${hostname}
::1 ip6-localhost ip6-loopback
::1 ${hostname}
```
执行`nano /etc/locale.gen`编辑本地化配置文件，删除以下行前面的#，可以使用Page Up和Page Down健来快速翻页上下滚动。
```
#en_US.UTF-8 UTF-8
#zh_CN.UTF-8 UTF-8
#zh_TW.UTF-8 UTF-8
```
如果你需要其他本地化，删除其他行前的#，行末尾务必是 UTF-8。最后保存退出，执行`locale-gen`生成本地化环境。

设置默认本地化环境：`echo LANG=en_US.UTF-8 > /etc/locale.conf`，为了确保兼容性，默认本地化环境最好是英语。

执行`SUDO_EDITOR=nano visudo`编辑 sudo 配置文件，删除`# %wheel ALL=(ALL) ALL`行前面的`#`以便让 wheel 用户组可以使用sudo获取 root 权限。

务必将下列命令中的${username}替换为你的用户名（例如：pikachu）。用户名建议使用小写字母组成（不能包含大写字母、不能以数字开头），最多 32 个字符，你可以使用下面的输入框验证用户名是否符合规则。

请输入用户名
创建一个新用户并加入 wheel 用户组：`useradd -m -G wheel -s /bin/bash ${username}`

设置新用户密码：`passwd ${username}`

PS：输入密码时不会显示

安装引导管理程序（重要）：
安装 GRUB 软件包：`pacman -S grub`

安装 GRUB 至 ESP 分区：`grub-install --removable --target=x86_64-efi --efi-directory=/boot`，提示`Installation finished. No error reported.`字样表示安装成功。

生成 GRUB 配置文件：`grub-mkconfig -o /boot/grub/grub.cfg`

PS: 如果你电脑的 UEFI 固件不支持 64 位 UEFI 文件，则需要安装 32 位 UEFI 文件才能成功引导：`grub-install --removable --target=i386-efi --efi-directory=/boot`

如果你不喜欢 GRUB，或者喜欢简单的引导管理程序，可以尝试下 systemd-boot。

退出新系统环境：`exit`

执行`umount -R /mnt`卸载分区，如果没有错误，拔掉 ArchLinux 安装盘并执行reboot重启系统。

没有意外的话，应该成功启动至新系统并看到以下界面，login 前面显示的是主机名，输入刚刚创建的新用户名和密码登陆系统。

成功启动

PS：如果你有多个系统，可能并不会重启至 ArchLinux，需要你手动选择 ArchLinux 启动。

进阶设置
root 权限小贴士
sudo 是临时获取 root 权限的工具，如果需要 root 权限执行某个命令，可以使用 sudo 执行它。sudo 提供了细粒化的控制，使它更加的安全。

虽然su也可以获取 root 权限，但是正常情况下你不应该使用它，相较 sudo，它太暴力了。如果没有设置 root 密码，su也将无法工作。

一些命令内置了独立的提权实现，比如systemctl命令，当这些命令需要 root 权限，但是没有以 root 身份允许时，它们就会使用自己的提权实现进行提权操作。

可以使用sudo -i临时登陆到 root 用户的 Shell。

root 虽好，但不要滥用，使用之前应该先了解当前命令的操作是否真的需要 root 权限，滥用 root 权限轻则会导致一些程序无法正常工作，重则会危及系统安全。

PS：ArchLinux 的 root 用户默认处于无密码锁定状态，除非你清楚自己在做什么，否则不建议解锁或设置密码。

启用网络管理器（注意大小写）：`sudo systemctl enable --now NetworkManager`

如果是无线网络，执行nmtui使用图形界面进行配置，选择「Activate a connection」连接无线网络。

如果你还想继续使用 SSH 远程控制，执行`sudo pacman -S openssh`安装 OpenSSH，然后执行`sudo systemctl start sshd`启动 SSH 服务器。

不过此时就不能使用 root 用户登陆 SSH 了，需要使用刚刚创建的新用户。

禁用网络管理器的连接等待（注意大小写）：`sudo systemctl disable NetworkManager-wait-online`，禁用后可以加快系统启动，不会有副作用，除非某些自动启动的服务需要互联网。

启用从网络同步系统时间：`sudo timedatectl set-ntp true`

启用本地 DNS 解析器（可选，建议启用）：`sudo systemctl enable --now systemd-resolved`

创建 DNS 解析文件软链接（依赖上一个操作）：`sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf`

启用每周自动清理不需要的 Pacman 软件包缓存（可选，建议启用）：`sudo systemctl enable paccache.timer`

如果你使用的硬盘支持 TRIM 特性（大多数固态硬盘都支持），建议启用定时执行 TRIM：`sudo systemctl enable fstrim.timer`

如果你需要使用 Wine、Steam 或其他的 32 位程序，执行`sudo nano /etc/pacman.conf`编辑 Pacman 配置文件，删除以下行前面的#以启用 32 位系统库支持。
```
#[multilib] 
#Include = /etc/pacman.d/mirrorlist
```
保存后执行sudo pacman -Sy更新软件数据库

添加 ArchLinuxCN 存储库（推荐）：
Arch Linux 中文社区仓库是由 Arch Linux 中文社区驱动的非官方软件仓库，包含许多官方仓库未提供的额外的软件包，以及已有软件的 git 版本等变种。一部分软件包的打包脚本来源于 AUR，但也有许多包与 AUR 不一样。（关于 AUR 的介绍请见下面的章节）

ArchLinuxCN 依靠镜像加速可以在国内提供比 AUR 更好的体验，而且还省去了部分软件的编译过程。如果你的网络环境导致无法顺利从 AUR 安装某些软件包，推荐添加。

执行`sudo nano /etc/pacman.conf`编辑 Pacman 配置文件，在底部添加以下行。
```
[archlinuxcn]
Server = https://opentuna.cn/archlinuxcn/$arch
```
保存后执行`sudo pacman -Sy archlinuxcn-keyring`更新软件数据库并安装 ArchLinuxCN GPG 密钥，如果安装命令出现`error: archlinuxcn-keyring: Signature from "Jiachen YANG (Arch Linux Packager Signing Key) " is marginal trust`这样的错误，执行命令`sudo pacman-key --lsign-key "farseerfc@archlinux.org"`修复，然后再重新执行安装命令。

安装防火墙支持（可选）：
如果你需要在非信任的网络下使用设备，推荐使用防火墙增强你的设备网络安全性，如果你只在你信任的网络下使用设备，那么可以不安装。

防火墙有很多选择，ArchLinux 的基础包默认已经包含了 iptables 实用工具，用于配置 Linux 内核实现的防火墙，不过由于它并不易用，我推荐最好是使用易用的 iptables 前端。

我推荐大家安装 Firewalld，它简单易用，并且我们待会安装的桌面环境可以很好的集成它，便于使用图形界面管理。其他 iptables 前端可参考：https://wiki.archlinux.org/title/Iptables#Front-ends

安装防火墙：`sudo pacman -S firewalld`

启用防火墙：`sudo systemctl enable --now firewalld`

Firewalld 更多信息：https://wiki.archlinux.org/title/Firewalld

###安装显卡驱动、Vulkan 和硬件加速支持：
如果不知道该装那个，可以加入 QQ 群向我询问。

小山只实际测试过 AMD 和 Intel 的显卡驱动安装，Nvidia 显卡驱动安装尚未测试。

Vulkan 是跨平台图形接口，目前主要用途是在 Linux 下运行 Windows 游戏以及部分 Linux 游戏（详见：Linux 运行 Windows 游戏指南），但并不是所有显卡都支持它，你可以前往 https://vulkan.gpuinfo.org/listreports.php?platform=linux 查询你的显卡是否支持 Vulkan。

OpenCL 是可选的，推荐需要时再安装。

如果你没有启用 32 位系统库支持，执行下列命令时务必删除lib32开头的软件包。

####AMD
#####图形加速库
`sudo pacman -S mesa`

#####硬件视频加速
`sudo pacman -S mesa-vdpau libva-mesa-driver lib32-mesa lib32-mesa-vdpau lib32-libva-mesa-driver lib32-opencl-clover-mesa`
**注：原文 `lib32-opencl-mesa`已不适用,换用`ib32-opencl-clover-mesa`**

#####Vulkan：
`sudo pacman -S vulkan-icd-loader vulkan-radeon lib32-vulkan-icd-loader lib32-vulkan-radeon`

#####OpenCL
`sudo pacman -S ocl-icd`
**注：原文`opencl-mesa`已不适用，废除。**

andvlk 是由 AMD 开源的 AMD GPU Vulkan 实现，这个实现比 Mesa 的 RADV 在某些场景下性能要好一些，并且系统允许安装多个 Vulkan 实现并让软件进行选择，推荐游戏玩家安装。

####Intel
#####图形加速库
`sudo pacman -S mesa`

硬件视频加速：如果你的 Intel CPU 架构是 Broadwell 或以上（包括 Intel Arc 显卡）的安装intel-media-driver，反之则安装libva-intel-driver。

#####Vulkan
`sudo pacman -S vulkan-icd-loader vulkan-intel lib32-mesa lib32-vulkan-icd-loader lib32-vulkan-intel`

OpenCL：如果你的 Intel CPU 架构是 Broadwell 或以上（包括 Intel Arc 显卡）的安装intel-compute-runtime ocl-icd，反之则安装beignet ocl-icd，beignet 需要通过 AUR 安装。

####Nvidia
前往 https://www.nvidia.com/en-us/drivers/unix/legacy-gpu/ 查看 Nvidia 私有驱动最新版本不支持的显卡列表，如果你的显卡不在这个列表里，那么你可以使用最新的 Nvidia 私有驱动，反之则需要使用开源驱动或旧版私有驱动（通过 AUR 安装）。

#####Nvidia 私有驱动
`sudo pacman -S nvidia nvidia-settings cuda xorg-server-devel lib32-nvidia-utils`

#####Nvidia 开源驱动 (RTX 3000 系列或更高)
`sudo pacman -S nvidia-open nvidia-settings cuda xorg-server-devel lib32-nvidia-utils`

#####OpenCL
`sudo pacman -S opencl-nvidia ocl-icd lib32-opencl-nvidia`

Nouveau 是 Linux 内核的 Nvidia 显卡开源驱动，由于 Nvidia 新显卡需要签名固件，所以它无法支持一些较新系列的显卡，推荐 GTX 700 或以下系列使用。

#####Nouveau 图形加速库
`sudo pacman -S mesa`

#####Nouveau 硬件视频加速
`sudo pacman -S mesa-vdpau libva-mesa-driver lib32-mesa lib32-mesa-vdpau lib32-libva-mesa-driver`

PS：如果是双显卡的笔记本，运行软件时可以选择使用的显卡（默认是集成显卡）。参见：https://wiki.archlinux.org/title/PRIME

如果你不需要桌面环境，那么你的安装到这里就可以结束了，也可以看看下面的 AUR 介绍和推荐阅读。
安装显示管理器和桌面环境：
本指南以 KDE Plasma 桌面环境 + SDDM 显示管理器为例

Plasma 是功能全面且对新手友好的桌面环境，具有高度可定制性，并且它在最近的更新中，性能提升了很多。

SDDM 显示管理器是 Plasma 推荐的显示管理器

如果你想使用其他桌面环境或显示管理器，建议前往其他桌面环境或显示管理器的 ArchWiki 页面详细了解

桌面环境列表：https://wiki.archlinux.org/title/Desktop_environment#List_of_desktop_environments

显示管理器列表：https://wiki.archlinux.org/title/Display_manager#List_of_display_managers

#####安装桌面环境、显示管理器、终端（Konsole）以及 Noto 字体集
`sudo pacman -S --noconfirm plasma sddm konsole noto-fonts noto-fonts-cjk noto-fonts-emoji`

#####安装 Plasma 可选依赖（使其更好的工作）
`sudo pacman -S --asdeps gst-plugins-good packagekit-qt5 xsettingsd`

如果你是笔记本用户，推荐安装fwupd以便通过 LVFS 接受来自厂商的固件更新（需要厂商支持），以及安装power-profiles-daemon获取电源配置文件支持（需要设备支持）。

可能无用的软件包
plasma 是个软件包组，里面可能会有一些用不上的软件包。

以下软件包根据需求决定是否需要卸载

drkonqi: KDE 崩溃处理程序

kgamma5: 调整显示器的伽玛设置（已不存在）

ksshaskpass: ssh-add 密码助手

plasma-firewall: 防火墙管理界面

plasma-sdk: Plasma 开发工具包

plasma-thunderbolt: Thunderbolt（雷电）设备管理界面

plasma-vault: 创建加密的保险库（需要额外的可选依赖）

plasma-workspace-wallpapers: Plasma 桌面的额外壁纸

卸载以上列出的软件包：sudo pacman -Rns drkonqi ksshaskpass plasma-firewall plasma-sdk plasma-thunderbolt plasma-vault plasma-workspace-wallpapers

#####安装蓝牙支持（可选）：
安装：sudo pacman -S bluedevil

安装可选依赖（蓝牙音频支持）：sudo pacman -S --asdeps pulseaudio-bluetooth

启用：`sudo systemctl enable bluetooth`

设置交换空间（可选）
交换空间（Swap）是硬盘上的一块区域，通俗的讲就是虚拟内存，跟 Windows 下的虚拟内存（分页文件）是一样的，当物理内存不够用时内核会把部分物理内存数据转移到交换空间，从而使需要更多内存的程序可以运行。

交换空间是可选的，如果你的物理内存不够大，那么你可以使用交换空间使内存利用更高效，如果你的物理内存足够使用，也可以不需要使用交换空间。

由于交换空间是使用硬盘作为存储介质，所以它无法达到像物理内存的速度，特别是在机械硬盘或 SATA 固态硬盘上面，如果硬盘速度太慢，会使交换空间弊大于利。

可能某些人会告诉你虚拟内存的大小务必是物理内存的两倍，无论是 Windows 还是 Linux，这种说法是完全错误的，虚拟内存的大小完全根据需求决定（Windows 默认会自动管理虚拟内存大小）。

如果要使用交换空间，你也可以尝试一下 Zswap 和 ZRAM，两者皆在物理内存中提供压缩的交换空间，从而使物理内存可以容纳更多的数据，减少硬盘上的交换空间的 I/O 操作，有效延长固态硬盘寿命，代价是使用更多的 CPU。

注意：当你启用了交换空间，部分桌面的电源菜单会出现「休眠」选项，但是它并不会工作，因为休眠需要配置内核参数以及 initramfs hook。参见：https://wiki.archlinux.org/title/Power_management/Suspend_and_hibernate#Hibernation

交换分区 VS 交换文件：
交换空间用硬盘上的一块区域作为虚拟内存，这块区域既可以是一个文件，也可以是一个完整的分区。

以前 Linux 安装会建议为交换空间创建一个单独的分区，以确保交换空间的最佳性能。这种做法是因为在当时大多数电脑还是机械硬盘，机械硬盘起始扇区的速度可能会更快，完整的分区还可以避免碎片等问题。但是缺点也很明显，一旦建立分区，调整大小会非常麻烦，自由度较低。

现在的电脑大多数都使用固态硬盘，没有了机械硬盘的一些限制，所以更推荐使用交换文件，可以使交换空间更加灵活。

推荐使用 systemd-swap 来管理交换空间、Zswap 或 ZRAM，它可以根据当前内存和交换空间的使用情况自动创建新的交换空间，同时也可以比较方便的管理 Zswap 和 ZRAM。不过由它创建的交换文件并不能用于系统休眠，如果你有系统休眠需求，建议手动创建。

更多关于交换空间请参考：https://wiki.archlinux.org/title/Swap

更改显示管理器（SSDM）本地化环境（可选）：
如果要让 SDDM 的本地化环境为非英文，可以执行以下更改。

PS：本地化环境除了影响语言显示，还有时间格式。

执行`sudo SYSTEMD_EDITOR=nano systemctl edit sddm`编辑 SDDM 服务单元，在文件第 3 行添加：
```
[Service]
Environment=LANG=zh_CN.UTF-8
```
如果是其他语言用户，可以把zh_CN替换为你需要的本地化。

执行`sudo systemctl enable sddm`启用 SDDM

执行sudo reboot重启系统，重启后应该会看到如下图所示的界面，输入密码登陆桌面环境。

如果没有看到如下图所示的界面，可能是显卡驱动的安装有问题。

SSDM

 

###Plasma 桌面环境配置
点击屏幕左下角的按钮打开应用启动器，点击「System Settings」打开系统设置。

点击「Regional Setting」，点击系统设置界面右下角的「Add languages...」添加简体中文和繁体中文，并把英文排到最后，简繁排第一和第二，然后点击右下角的「Apply」。点击左侧的「Formats」，选择「Regional」为「中国 - 简体中文 (zh_CN)」，或者选择你所在的区域，然后点击右下角的「Apply」。

PS：简繁的优先级顺序取决于个人，因为有些程序可能没有简体或繁体翻译，简繁同时存在可以在没有其中一种翻译的情况下使用另一种翻译，如果两个都没有再使用英文。

按下Ctrl + Alt + Delete，选择「Log Out」注销会话并重新登录使其生效。

注：如果注销后发现图形界面为半中半英的，可以把`~/.config/plasma-localerc`中`Formats`的`zh_CN`修改为`zh_CN.UTF-8`.并注销桌面。

在应用启动器里找到「Konsole」，也可以通过快捷键Ctrl + Alt + T打开，这是 Plasma 桌面下的终端。

安装输入法、浏览器和其他实用软件
你也可以完全按照偏好去选择其他软件，或者不安装，下面有每个软件包的介绍。

安装：`sudo pacman -S --noconfirm dolphin ark kwrite gwenview fcitx5-im fcitx5-chinese-addons fcitx5-pinyin-zhwiki firefox firefox-i18n-zh-cn firefox-i18n-zh-tw`

安装 dolphin 可选依赖（视频缩略图支持）：`sudo pacman -S --asdeps ffmpegthumbs`

安装 ark 可选依赖（更多压缩包格式支持）：`sudo pacman -S --asdeps lrzip lzop p7zip unrar`

安装 gwenview 可选依赖（更多图片格式支持）：`sudo pacman -S --asdeps qt5-imageformats`

软件包介绍
dolphin = 文件管理器、ark = 压缩工具、kwrite = 文本编辑器、gwenview = 图片浏览器，这些软件都属于 KDE 家族，搭配 KDE Plasma 桌面使用更佳。

fcitx5-im: Fcitx5 输入法框架的软件包组（或许你喜欢 IBus？）

fcitx5-chinese-addons: Fcitx5 中文插件集合，包含拼音输入法等插件。

fcitx5-pinyin-zhwiki: 来自 zh.wikipedia.org 的 Fcitx5 拼音输入法字典

更多基于 Fcitx5 的中文输入法：https://wiki.archlinux.org/title/Fcitx5#Chinese

firefox: FireFox 浏览器

firefox-i18n-zh-cn firefox-i18n-zh-tw: FireFox 的简繁中文语言包，你也可以只安装需要的语言包。

如果你更偏爱使用 Chromium / Google Chrome 浏览器，可以安装chromium软件包。

PS：Chromium 是开源版的 Google Chrome，它们的区别参见 https://en.wikipedia.org/wiki/Chromium_(web_browser)#Differences_from_Google_Chrome。Google Chrome 可以通过 AUR 或 ArchLinuxCN 安装。

PPS：2021年3月15日起 Chromium 不再支持与 Google API 相关的功能，包括 Google 账户登陆和同步等。如果你需要使用这些功能，最佳方法是安装 Google Chrome，还有一种方法可以使 Chromium 恢复这些功能支持，你可以前往 ArchWiki 了解或加入 QQ 群向我询问。

更多 KDE 家族应用：apps.kde.org

执行nano ~/.bash_profile配置输入法环境变量，添加以下行：
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export SDL_IM_MODULE=fcitx
```
PS：输入法需要重启系统或重新登录才会生效

输入法默认激活快捷键：Ctrl + 空格

输入法的设置可以在 「系统设置」→「区域设置」→「输入法」修改，你可以点击「添加输入法」来添加拼音或其他输入法。

Plasma 设置优化
外观 → 字体：除了等宽字体，其余字体修改为 [Sans Serif]（无衬线）。如果你觉得字体看起来不正常（对比下图），可以加入 QQ 群与我交流。

开机和关机 → 登陆屏幕 (SDDM)： 主题更改为「微风」

Discover 可以用于安装/更新/卸载系统软件包和 Plasma 插件/主题，还有来自 LVFS 的固件更新。

删除安装过程中下载的软件包：`sudo pacman -Scc --noconfirm`

重启系统开启你的 ArchLinux 之旅

ArchLinux For Plamsa

#### 安装 AUR 软件包管理器（可选）
什么是 AUR？

AUR 全称是 Arch User Repository（Arch 用户软件仓库），AUR 软件包由来自社区的用户进行维护，他们上传编写好的软件包构建脚本，其他用户可以使用这些构建脚本自动生成软件包并安装。

官方仓库没有的软件包，或者是官方仓库软件包的开发或过时版本，基本上都可以在 AUR 找到，所以 ArchLinux 安装第三方软件包非常方便。

AUR: aur.archlinux.org

ArchWiki 的 AUR 页面：https://wiki.archlinux.org/title/Arch_User_Repository

PS：AUR 的软件包大多数都没有经过测试，使用风险需要自己承担。

AUR 软件包的安装和更新默认需要手动操作，并不能像使用 pacman 那样方便的安装和更新，不过一些优秀的 AUR 包管理工具可以让你像使用 pacman 安装存储库包那样安装 AUR 包。

我推荐使用 yay 作为 AUR 包的管理工具，因为它的使用方法和 pacman 基本一致，可以大幅度降低学习成本。

不过还是推荐大家学习如何手动安装 AUR 软件包，以备不时之需，下面安装 yay 的操作就是安装大多数 AUR 软件包的步骤。

如果是第一次安装 AUR 软件包，需要安装所需依赖：sudo pacman -S --noconfirm --needed base-devel git

安装 yay 步骤：

前往 AUR 的 yay 页面查看 「Git Clone URL」：https://aur.archlinux.org/packages/yay

使用 git 克隆存储库：git clone https://aur.archlinux.org/yay.git ~/aur-yay-repo

转到存储库目录：cd ~/aur-yay-repo

构建软件包并安装：makepkg -i -r -s

清理工作：cd ~ && rm -rf ~/aur-yay-repo

现在可以使用 yay 来管理存储库软件包和 AUR 软件包了，如果已添加的存储库不存在对应的包，yay 会自动在 AUR 中搜索，如果找到的话就开始安装操作。

用法：

如果不加任何参数执行 yay，它默认执行更新所有软件包操作：yay

安装软件包：yay -S <package_name> or yay -S --aur <package_name> # 只从 AUR 获取包

更新所有软件包：yay -Syu

只更新 AUR 软件包：yay -Su --aur

只更新存储库软件包：yay -Syu --repo

yay 更多用法参考：https://github.com/Jguer/yay#readme

PS：如果你添加了 ArchLinuxCN 软件存储库，因为它包含了一些 AUR 软件包，所以你可以使用 pacman 安装某些 AUR 软件包。或者，你也可以使用--aur参数让 yay 只从 AUR 获取包，相反你也可以使用--repo参数让 yay 只从存储库获取包。

注意：不要使用 root 权限执行 yay 或任何 AUR 相关的命令，它们会在需要 root 权限时使用 sudo。

#### 关于多系统启动
如果你的系统分别安装在不同的硬盘，那么只需要在电脑启动的时候引导不同的硬盘就可以了。

你也可以通过安装os-prober使 GRUB 自动检测已安装的其他系统，并将其添加到 GRUB 启动菜单。安装完成后，添加GRUB_DISABLE_OS_PROBER=false到/etc/default/grub，重新生成 GRUB 配置文件：grub-mkconfig -o /boot/grub/grub.cfg。如果你又安装了其他系统，则需要再次重新生成 GRUB 配置文件。

关于 os-prober 更多用法以及注意事项：https://wiki.archlinux.org/title/GRUB#Detecting_other_operating_systems


#### 安装Xrdp（需要yay或paru)
```
yay -S xrdp
yay -S xorgxrdp-glamor #针对amd和intel集显的调用
pacman -S xorg-xinit
```
修改`/etc/X11/Xwrapper.config`添加如下内容：
```
allowed_users=anybody
needs_root_rights=no
```
再接下来，复制xinit配置文件到用户根目录：`cp /etc/X11/xinit/xinitrc ~/.xinitrc` ，并修改最后几行为：
```
#twm &
#xclock -geometry 50x50-1+1 &
#xterm -geometry 80x50+494+51 &
#xterm -geometry 80x20+494-0 &
#exec xterm -geometry 80x66+0+0 -name login
# 声音
PULSE_SCRIPT=/etc/xrdp/pulse/default.pa pulseaudio --daemonize=no
pulseaudio &

# kde-plasma桌面
export DESKTOP_SESSION=plasma
/usr/lib/plasma-dbus-run-session-if-needed startplasma-x11
```
启用相关服务并重启系统：
```
sudo systemctl enable xrdp.service
sudo systemctl enable xrdp-sesman.service
sudo reboot
```
如果xrdp无法弹出授权框，可以修改`/etc/polkit-1/rules.d/49-nopasswd_global.rules`为：
```
/* Allow members of the wheel group to execute any actions
 * without password authentication, similar to "sudo NOPASSWD:"
 */
polkit.addRule(function(action, subject) {
    if (subject.isInGroup("wheel")) {
        return polkit.Result.YES;
    }
});
```
来允许所有请求。

#### 建议阅读
建议阅读以下 Wiki 以便更好的使用 ArchLinux：

应用程序列表：https://wiki.archlinux.org/title/List_of_applications，你可以在这里按照用途类型找到可用软件。

一般建议：https://wiki.archlinux.org/title/General_recommendations

系统维护：https://wiki.archlinux.org/title/System_maintenance

systemctl 基本用法：https://wiki.archlinux.org/title/Systemd#Basic_systemctl_usage

pacman 软件包管理器：https://wiki.archlinux.org/title/Pacman，安装/卸载/更新软件全靠它。

CPU 调频：https://wiki.archlinux.org/title/CPU_frequency_scaling

蓝牙：https://wiki.archlinux.org/title/Bluetooth

Synaptics 触摸板：https://wiki.archlinux.org/title/Touchpad_Synaptics，建议 Synaptics 触摸板的笔记本用户阅读。

Bash：https://wiki.archlinux.org/title/Bash

腾讯 QQ：https://wiki.archlinux.org/title/Tencent_QQ

中国大陆用户的推荐解决方案：https://wiki.archlinux.org/title/General_recommendations#中国大陆用户的推荐解决方案



  [1]: https://www.mivm.cn/archlinux-installation-guide-simple