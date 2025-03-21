---
title: "PVE安装后续优化"
categories: [ "记录" ]
tags: [ "linux","PVE","nas" ]
draft: false
slug: "pve_install"
date: "2023-03-12 01:43:00"
---

安装PVE并不复杂，相关操作网上一抓一大把此处不再赘述。
此处主要记录一些安装后碰上的问题以及对应的解决方案。
#####<center>开启硬件直通</center>#####
1.首先确保CPU带有虚拟化能力。对于AMD平台，在`BIOS的高级>CPU设置`查看是否有能开启`SVM Mode`。![2023-03-11T17:25:44.png][1]确认能打开后，到`高级>AMD CBS>NBIO Common Options>NB Configuration`下把`IOMMU`打开。![2023-03-11T17:27:47.png][2]
2.进入系统控制台，修改grub启动时添加硬件直通功能。
打开`/etc/default/grub`
将
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
```
进行修改。

对于AMD平台修改为
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on  iommu=pt video=efifb:off,vesafb:off"
```
对于Intel平台修改为
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt video=efifb:off,vesafb:off" 
```
修改完成后执行：
```
update-grub
```
并添加以下模块到内核中：
```
echo vfio >> /etc/modules
echo vfio_iommu_type1 >> /etc/modules
echo vfio_pci >> /etc/modules
echo vfio_virqfd >> /etc/modules
```
添加完成后重启。
#####<center>替换源</center>#####
执行以下命令：
```
#先把企业源换掉
rm -rf /etc/apt/sources.list.d/pve-install-repo.list 
echo "#deb https://enterprise.proxmox.com/debian/pve Bookworm pve-enterprise" > /etc/apt/sources.list.d/pve-enterprise.list
#替换其他软件源
wget https://mirrors.ustc.edu.cn/proxmox/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg 
echo "deb https://mirrors.ustc.edu.cn/proxmox/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list 
echo "deb https://mirrors.ustc.edu.cn/proxmox/debian/ceph-pacific bookworm main" > /etc/apt/sources.list.d/ceph.list 
sed -i.bak "s#http://download.proxmox.com/debian#https://mirrors.ustc.edu.cn/proxmox/debian#g" /usr/share/perl5/PVE/CLI/pveceph.pm 
sed -i.bak "s#ftp.debian.org/debian#mirrors.aliyun.com/debian#g" /etc/apt/sources.list 
sed -i "s#security.debian.org#mirrors.aliyun.com/debian-security#g" /etc/apt/sources.list 
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" >>  /etc/apt/sources.list 
```
最后执行更新
```
apt update
```
#####<center>关闭订阅提示</center>#####
修改文件：
```
/usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
```
搜索 `data.status.toLowerCase`，并将框中内容删除：
![2023-03-11T17:42:05.png][3]
再次搜索 `data.status.toLowerCase`，并将框中内容删除：
![2023-03-11T17:42:59.png][4]
之后重启平台。
#####<center>无法使用mkfs格式化硬盘</center>#####
如果提示：
```apparently in use by the system; will not make a filesystem here!```
则先执行
```dmsetup remove_all```
再执行
```mkfs.filesystem /dev/disk```
#####<center>手动挂载已有硬盘</center>#####
首先通过```fdisk -l```查询硬盘/分区的路径
如需要挂载的设备为：
```/dev/sdc4  83888128 468862094 384973967 183.6G Linux filesystem```
接下来通过```blkid``查询设备UUID及分区类型
如查询结果为：
```/dev/sdc4: UUID="b958d02b-9f99-4e48-bacf-xxxxxxxx" BLOCK_SIZE="512" TYPE="xfs" PARTUUID="beabe66d-07c2-2d4b-9836-xxxxxxx"```
如果需要挂载的是硬盘，接下来需要的是UUID，如果需要的是分区，则需要PARTUUID，另一个参数`type="xfs"`表明了文件系统类型
修改```/etc/fstab```添加以下内容
```
UUID="b958d02b-9f99-4e48-bacf-xxxxxxxx" /mnt/rootfs2 xfs defaults 0 0
```
第二个参数是需要挂载的目录位置。最后执行
```mount -a```即可。
#####<center>使用lxc安装docker</center>#####
1、首先进行换源操作
```
cp /usr/share/perl5/PVE/APLInfo.pm /usr/share/perl5/PVE/APLInfo.pm_back
sed -i 's|http://download.proxmox.com|https://mirrors.tuna.tsinghua.edu.cn/proxmox|g' /usr/share/perl5/PVE/APLInfo.pm
```
更换完成后重启pve。
2、接下来进入PVE网页端，按如下操作安装debian软件包。（选择的目录应有CT镜像功能）![2023-03-12T08:51:14.png][5]之后等待窗口提示`TASK OK`。
3、点击右上角的`创建CT`，配置常规选项：特别要记住`CT ID`，并把`无特权的容器`的勾取消掉。![2023-03-12T08:56:05.png][6]
模板刚才下载的目录位置以及模板。
![2023-03-12T08:56:45.png][7]
磁盘空间根据实际情况给，建议20G以上。
![2023-03-12T08:57:28.png][8]
核心数分配根据机器的实际情况配置。
![2023-03-12T08:57:56.png][9]
内存设置考虑到后期我需要运行的Docker内容较多，我设置为1024，如果配置较低或者使用负载较小可以设置512，Swap同理。
![2023-03-12T08:59:05.png][10]
网络选项根据自己内网路由情况进行编辑，不建议使用DHCP，因为我经常找不到IP。
![2023-03-12T09:00:35.png][11]
DNS设置使用默认即可。然后确认信息无误点击完成，等待`TASK OK`。
![2023-03-12T09:01:25.png][12]
然后进入Docker的容器选项设置，把功能中以下内容勾选。
![2023-03-12T09:02:53.png][13]
接下来进入终端，修改配置文件，如我的lxc容器id是`100`，则修改
```/etc/pve/lxc/100.conf```
在文件末尾添加以下内容：
```
lxc.apparmor.profile: unconfined  
lxc.cgroup.devices.allow: a  
lxc.cap.drop:
#以上三行是必备的，以下两行是用来开启容器tun转发的，不是必须的

lxc.cgroup2.devices.allow: c 10:200 rwm  
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
```
修改后整个文件应该看起来像：
![2023-03-12T09:06:19.png][14]
接下来点击Docker界面的启动，等待启动完成。
4、进入Docker的控制台，修改软件源：
```
修改 /etc/apt/sources.list替换成以下内容：
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free

```
执行`apt update`更新。
5、
执行以下命令：
```
apt install docker.io
docker run -d --restart=always --name="portainer" -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data 6053537/portainer-ce
```
进入容器IP:9000即可看到Docker控制界面。
#####<center>开机提示ACPI BIOS Error(bug)...</center>#####
在引导界面按e，并在linux行的最后添加```acpi_backlight=vendor```
这种情况一般是修改显卡直通之后出现的，而在网上查了一圈，好像也只有华擎主板有这个问题...[参考链接][15]

#####<center>挂载现有硬盘到虚拟机或lxc容器</center>#####
先获取分区的识别符
```ls -l /dev/disk/by-id```
例如本次要挂载的分区为`ata-b958d02b-9f99-4e48-bacf-xxxxxxxx`
如果要挂载到虚拟机上，则执行
```qm set 虚拟机ID -sata1 /dev/disk/by-id/硬盘识别符```
如果要挂载到LXC容器内，则执行
```pct set 容器ID -mp0 /dev/disk/by-uuid/硬盘识别符,mp=/挂载路径```
如果已经在pve上挂载硬盘想映射进lxc容器，可以执行：
```pct set 容器ID -mp0 /pve上挂载位置,mp=/lxc挂载路径```
同理可将pve上的任意路径挂载进容器中。
#####<center>PVE8.0创建LXC容器之后无法进入ssh</center>#####
更新到8.0之后发现怎么创建容器都进不去SSH，具体是：控制台一片空白；XSHELL连接提示拒绝访问；宿主机使用`ssh root@192.168.xx.xx`提示`Permission denied, please try again.`（密码是正确的）
这种情况下可以先进入宿主机的ssh，执行:
```#pct list
VMID       Status     Lock         Name                
101        running                 docker12            
```
当前我运行的lxc容器ID是101，因此执行：
```# pct enter 101
root@docker12:/etc/ssh#
```
这样就能进来了。
再编辑下`/etc/ssh/sshd_config`，把`PermitRootLogin`改成`yes`，再重启下sshd服务，这次能正常登陆了。


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640cb997ec1de.png
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640cba128c724.png
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640cbd6c8417c.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640cbda27355a.png
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d92828b077.png
  [6]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d93a5acf5c.png
  [7]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d93cd17138.png
  [8]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d93f8bccc7.png
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d9413db27b.png
  [10]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d9459ae6d2.png
  [11]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d94b3dfa2c.png
  [12]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d94e514dbc.png
  [13]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d953d9bdd1.png
  [14]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/03/12/640d960b5a827.png
  [15]: https://post.smzdm.com/p/akko88w8/