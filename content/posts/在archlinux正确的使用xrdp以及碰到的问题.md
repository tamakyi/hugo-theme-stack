---
title: "在archlinux正确的使用xrdp以及碰到的问题"
categories: [ "记录" ]
tags: [ "archlinux","xrdp" ]
draft: false
slug: "archlinux_xrdp"
date: "2024-02-05 23:58:41"
---

为什么不用vnc？一是需要另外装个软件，不方便，而且配置麻烦，总是能碰到七七八八的问题，而用xrdp基本上用巨硬自带的软件就能实现远程连接。
#### 安装并配置xrdp
**注：我是kde桌面环境**
```shell
sudo pacman -S xorg-xinit
sudo pacman -S base-devel
yay -S xrdp
```
以上是基础软件包，下面根据平台选择安装：
```shell
yay -S xortxrdp-glamor  #适用于I家和A家核显解码
yay -S xorgxrdp   #使用CPU编码
yay -S xordxrdp-nvidia   #使用英伟达独显解码
```
修改`/etc/X11/Xwrapper.config`（无此文件自行新建），添加如下内容：
```shell
allowed_users=anybody
needs_root_rights=no
```
复制一个.xinitrc到用户主目录并编辑其中内容:
```shell
cp /etc/X11/xinit/xinitrc /home/$USERNAME/.xinitrc
```
注释掉最后的几行 
```shell
#twm &
#xclock -geometry 50x50-1+1 &
#xterm -geometry 80x50+494+51 &
#xterm -geometry 80x20+494-0 &
#exec xterm -geometry 80x66+0+0 -name login
export DESKTOP_SESSION=plasma
/usr/lib/plasma-dbus-run-session-if-needed startplasma-x11 
```
最后两行根据桌面环境替换：
i3w / dwm:
```shell
exec i3w
```
gnome
```shell
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec dbus-launch  gnome-shell --x11
```
xfce4
```shell
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec dbus-launch startxfce4
```
启动xrdp服务：
```shell
sudo systemctl enable xrdp
sudo systemctl start xrdp
```
#### 一些小问题：
##### 安装以后，黑屏、闪退

 1. 可以尝试修改`/etc/xrdp/xrdp.ini`，把`allow_channels=true`改成`allow_channels=false`
 2. 使用`ps -elf|grep vnc`查询是否有多个进程同时运行，如果有，直接`kill -9 $pid`（为避免出现这种问题建议每次都注销而不是直接叉掉rdp窗口）

##### 权限（输入密码）弹窗不出来
新增`/etc/polkit-1/rules.d/49-nopasswd_global.rules`文件，并添加以下内容：
```shell
/* Allow members of the wheel group to execute any actions
 * without password authentication, similar to "sudo NOPASSWD:"
 */ 
polkit.addRule(function(action, subject) {
    if ((  action.id == "org.freedesktop.policykit.exec"
    || action.id == "org.fedoraproject.FirewallD1.all"
    || action.id == "org.fedoraproject.FirewallD1.config"
    || action.id == "org.freedesktop.NetworkManager.settings.modify.system") &&
      subject.isInGroup("wheel")) {
        return polkit.Result.YES;
    }
});
```
修改后会默认允许所有授权，可能存在一定风险。

##### 修改3389端口
修改配置文件`/etc/xrdp/xrdp.ini`中`port=3389`字段即可，如果有开防火墙记得开放端口。
