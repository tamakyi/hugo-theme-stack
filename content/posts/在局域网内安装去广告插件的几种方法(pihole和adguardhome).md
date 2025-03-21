---
title: "在局域网内安装去广告插件的几种方法(pihole和adguardhome)"
categories: [ "记录" ]
tags: [ "pihole","dockcer","去广告","adguardhome" ]
draft: false
slug: "ad-plugin"
date: "2024-04-13 23:37:00"
---

这东西我主要是放在家里给老人用的，最开始是他们手机碰上七七八八的问题找我解决，然后点开一个个浏览器和应用，卧槽，这能用的下去？横幅的横幅，弹窗的弹窗，真的看不下去，正好家里整了一个24小时开机的J1900双网口软路由，于是想了想在上面安装个插件算了，怎样都比原来好。


<!--more-->

### PiHole on Docker

#### 安装之前
首先需要打开主机的ssh，执行
```ip link set eth0 promisc on```
打开网卡混杂模式，这个步骤是必须的，不然不能接收到域名解析请求。
然后新建一个网络，网段和网关要和主机所在的网段一致。比如说宿主机的IP是`192.168.101.232`，那么新建的网络子网则为`192.168.101.0/24`，网关为`192.168.101.1`，还需要把驱动器切换成`MAC VLAN`，基设备修改成`br-lan`，其它可以不变，`IPV6`打不打开都可以，打开之后需要配置和宿主机同级的IPV6的网段和网关。
创建好网络之后下一步就是下载镜像和新建容器，镜像直接拉取`pihole/pihole`，最新的。
然后在主机上随便一个目标下新建`config`和`data`文件夹，用于存储pihole的相关数据。比如`/opt/pihole/data`和`/opt/pihole/config`。
接下来新建容器，选择pihole的镜像和前面新建的网络，分配一个网段内不冲突的地址，比如`192.168.101.102`，并添加如下绑定挂载：

    /opt/pihole/config:/etc/pihole
    /opt/pihole/data:/etc/dnsmasq.d

然后提交并启动。
点击容器后面的编辑->控制台->连接，输入

    sudo pihole -a -p

给pihole配置密码，然后访问`192.168.101.103/admin`看看是否能访问。

#### 配置Pi-Hole
一般情况下这里要做的就是三件事：

##### 修改上游DNS
位于`settings/dns`下的`Upstream DNS Servers Custom 1 (IPv4)`和`Custom 2 (IPv4)`，一般填写光猫获取到的运营商DNS，也可以写`8.8.8.8`这类，只要访问快就行，改完记得滑到底部点击`save`。

##### 添加拦截规则
位于`adlists`下，直接把拦截规则放进`address`下点击`add`即可。一般推荐

    https://anti-ad.net/domains.txt
    https://raw.githubusercontents.com/StevenBlack/hosts/master/hosts
    
这两个，第二个是pihole自带的，但是github部分地区无法访问，建议删掉原来的，再把上面这个镜像地址添加进去。添加完之后不会自动下载，需要到`tools/update gravity`下点击`update`手动更新一次。

##### 导入解析
如果是单个设备，直接到对应的连接选项从DHCP改成静态再把下面把DNS改成`192.168.101.103`即可。
如果是路由器（我的网络是光猫-软路由-硬路由），我用的是梅林，直接在内部网络(lan)下的	基本设置改成静态IP，配置好IP地址和网关，DNS服务器则修改成`192.168.101.103`保存即可。
如果成功了，Dashboard和Query Log应该能看到解析记录。

#### 这样做有什么好处
docker环境相对于直接在硬路由/软路由上搭建最大的好处就是不用担心53端口冲突，这点就够了

### Pi-hole Hardware

#### 安装之前
我的测试环境是安装过宝塔php8.1+8.2的64位树莓派raspios。如果是全新安装的可能有些步骤是不必要的。
安装前一定要确保机器能够访问到Github，如果不能请配置好代理。例如我在`192.168.118.214`上搭建了3proxy建议代理服务，端口分别是： `1079: http/https 1080:socks5`，因此需要配置临时代理：

    export https_proxy=http://192.168.118.214:1079 http_proxy=http://192.168.118.214:1079 all_proxy=socks5://192.168.118.214:1080

然后就可以执行安装了：

    curl -sSL https://install.pi-hole.net | sudo bash

脚本执行时会安装一系列的依赖，需要注意像php这类的软件包部分源只提供了最新版本，比如原来我只安装有8.1，结果在安装：

    [i] Checking for php8.1-common (will be installed)
    [i] Checking for php8.1-cgi (will be installed)
    [i] Checking for php8.1-sqlite3 (will be installed)
    [i] Checking for php8.1-xml (will be installed)
    [i] Checking for php8.1-intl (will be installed)

的时候全部都找不到，然后脚本就退出了...执行

    sudo apt search php8.*

可以看到很多都是8.2的包了...灰溜溜的去宝塔重新编译安装一个再重新执行脚本：

    php8.2/stable,stable,stable-security,stable-security 8.2.7-1~deb12u1 all
      server-side, HTML-embedded scripting language (metapackage)

    php8.2-amqp/stable 1.11.0-5 arm64
      AMQP extension for PHP

    php8.2-apcu/stable 5.1.22+4.0.11-2 arm64
      APC User Cache for PHP
    ...

安装过程有个简单的交互，无非就是问你是不是真的要安装，使用哪个网卡，使用哪个DNS，要不要装个lighthttp实现网页端管理这类的，基本上全部选是就对了。安装完成后会提示管理网页地址和默认密码，可以使用`pihole -a -p`命令更改：

      [i] Pi-hole blocking will be enabled
      [i] Enabling blocking
      [✓] Reloading DNS lists
      [✓] Pi-hole Enabled
      [i] Web Interface password: CPlutkpL
      [i] This can be changed using 'pihole -a -p'    

      [i] View the web interface at http://pi.hole/admin or http://192.168.118.164/admin    

      [i] You may now configure your devices to use the Pi-hole as their DNS server
      [i] Pi-hole DNS (IPv4): 192.168.118.164
      [i] Pi-hole DNS (IPv6): 2***********34
      [i] If you have not done so already, the above IP should be set to static.    

      [i] The install log is located at: /etc/pihole/install.log
      [✓] Installation complete! 
到这里就算安装完成了。

#### 一些问题
一般情况下现在就可以直接访问pihole了，但是你可能会发现管理页面报错404，如果你有安装过lnmp环境，这可能是80端口冲突导致lighthttp无法启动：

    root@akarinbot:~# systemctl status lighttpd.service 
    × lighttpd.service - Lighttpd Daemon
         Loaded: loaded (/lib/systemd/system/lighttpd.service; enabled; preset: enabled)
         Active: failed (Result: exit-code) since Mon 2024-04-15 23:09:25 CST; 5min ago
       Duration: 476ms
        Process: 173339 ExecStartPre=/usr/sbin/lighttpd -tt -f /etc/lighttpd/lighttpd.conf (code=exited, status=0/SUCCESS)
        Process: 173342 ExecStart=/usr/sbin/lighttpd -D -f /etc/lighttpd/lighttpd.conf (code=exited, status=255/EXCEPTION)
       Main PID: 173342 (code=exited, status=255/EXCEPTION)
            CPU: 886ms    

    4月 15 23:09:25 akarinbot systemd[1]: lighttpd.service: Scheduled restart job, restart counter is at 7.
    4月 15 23:09:25 akarinbot systemd[1]: Stopped lighttpd.service - Lighttpd Daemon.
    4月 15 23:09:25 akarinbot systemd[1]: lighttpd.service: Start request repeated too quickly.
    4月 15 23:09:25 akarinbot systemd[1]: lighttpd.service: Failed with result 'exit-code'.
    4月 15 23:09:25 akarinbot systemd[1]: Failed to start lighttpd.service - Lighttpd Daemon.

所以我们需要手动修改lighthttp的配置文件：
    root@akarinbot:~# nano /etc/lighttpd/lighttpd.conf
    ...
    #server.port                 = 80
    server.port                 = 88 #这里改成80外的其他端口
    ...

然后重启lighthttp服务：

    root@akarinbot:~# systemctl restart lighttpd.service

检查服务状态：

    root@akarinbot:~# systemctl status lighttpd.service

如果提示active (running)此时应该就完成了。如果timeout了，建议检查防火墙配置，要注意53端口的TCP/UDP都要打开哦。


### AdGuardHome on Hardware

#### 安装之前
需要注意的是，这个方法适用于使用openwrt的系统，并且支持安装luci软件包。
下载：
    
    https://github.com/rufengsuixing/luci-app-adguardhome/releases/download/1.8-11/luci-app-adguardhome_1.8-11_all.ipk
    
到本地，在openwrt内的`系统-软件包`下选择上传该软件包，无视警告并安装，然后刷新页面。
到：
    
    https://github.com/AdguardTeam/AdGuardHome/releases
    
下载适合自己系统版本的软件包。比如X86架构linux的：
    
    https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.107.48/AdGuardHome_linux_amd64.tar.gz
    
下载后解压到任意目录，解压并增加相关工作目录：

    cd ~
    wget https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.107.48/AdGuardHome_linux_amd64.tar.gz
    tar zxvf AdGuardHome_linux_amd64.tar.gz
    mkdir conf && mkdir data

此时Adguard的路径应该为`/root/AdGuardHome`。

#### 配置AdguardHome luci和AdguardHome
管理界面下的`服务-AdGuard Home`，把以下项目修改：

    6060重定向-->作为dnsmasq的上游服务器
    执行文件路径->/root/AdGuardHome/AdGuardHome
    配置文件路径->/root/AdGuardHome/conf/AdGuardHome.yaml
    工作目录->/root/AdGuardHome
    运行日志->/root/AdGuardHome/data

然后保存并应用，再勾选启用，再次保存，进入路由器IP:3000，配置AdGuardHome。
需要注意的是一般情况下53端口是被占用了，建议修改成5553。
进入AdGuardHome之后在`设置-DNS设置`下面把上游DNS服务器修改成：

    https://dns.alidns.com/dns-query
    tls://dns.alidns.com
    223.5.5.5

以上都是阿里云的DNS，实测我的网络解析速度还行。
Bootstrap DNS 服务器则修改为：

    223.5.5.5
    223.6.6.6
    114.114.114.114
    8.8.8.8

保存之后再去`过滤器-DNS黑名单`下启用需要的规则就行了，自带的几个都不错，打勾就能下载。

##### 导入解析
此步骤和使用PiHole一模一样。