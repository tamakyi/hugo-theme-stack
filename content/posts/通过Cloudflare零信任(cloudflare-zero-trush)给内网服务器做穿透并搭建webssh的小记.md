---
title: "通过Cloudflare零信任(cloudflare zero trush)给内网服务器做穿透并搭建webssh的小记"
categories: [ "记录" ]
tags: [ "cloudflare","zero trush","webssh" ]
draft: false
slug: "cloudflare_webssh_zerotrush"
date: "2023-08-18 14:23:00"
---

通过cloudflare zero trush可以非常快速且方便的搭建内网穿透，而且速度一般情况下可以接受，最重要的是成本很低，你只需要：

 1. 一个cloudflare账号
 2. 一个托管到cloudflare的域名

以上内容百度到处都是，不再赘述，以下仅记录搭建webssh的过程。


----------
####1、安装并配置tunnel
需要在ssh服务器上执行如下命令：
```
#安装cloudflared，我用的arch，其他平台自己替换。
sudo pacman -S cloudflared
#这一步执行后会得到一个链接，复制到浏览器打开，然后选择一个托管到cloudflare的域名，确认之后会将对应的pem证书推到你服务器的.cloudflare目录下。
cloudflared tunnel login
#创建一个tunnel，其中$tunnel-name自行配置
cloudflared tunnel create $tunnel-name
#查看tunnel列表
cloudflared tunnel list
```
此时在输出的列表中找到刚才创建的tunnel名为$tunnel-name对应的ID，一般格式为`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。
####2.创建配置文件。
进入用户目录下的`.cloudflared`文件夹，创建名为`config.yam`的文件，内容如下：
```shell
tunnel: $ID
credentials-file: /home/$username/.cloudflared/$ID.json
ingress:
  # ssh
  - hostname: ssh.example.com
    service: ssh://localhost
  # vnc远程桌面，端口5900
  - hostname: vnc.example.com
    service: tcp://localhost:5900
  # 其他http服务
  - hostname: file.example.com
    service: http://localhost:9527
  - hostname: speed-test.example.com
    service: http://localhost:9898
  # 如果都没有匹配，则返回404，这句不能少
  - service: http_status:404
```
其中$ID替换为上面的ID，$username为你服务器本地用户名，`hostname`字段对应外网访问的网址，`service`字段对应内网网址，如果是带端口的在后面增加即可。
####3.写一个脚本，自动更新DNS以及运行tunnel
`vim ~/tunnel-sh.sh`
内容如下：
```
# update cloudflare DNS record
/usr/bin/cloudflared tunnel route dns $tunnel-name ssh.example.com
/usr/bin/cloudflared tunnel route dns $tunnel-name vnc.example.com
/usr/bin/cloudflared tunnel route dns $tunnel-name file.example.com
/usr/bin/cloudflared tunnel route dns $tunnel-name speed-test.example.com
# run tunnel
/usr/bin/cloudflared tunnel run $tunnel-name
```
注意把$tunnel-name和网址换成自己的。
####4.开机自启
新建一个service，
```sudo vim /etc/systemd/system/cloudflared-tunnel.service```
内容如下：
```shell
[Unit]
Description=cloudflared tunnel
Wants=network-online.target
After=network-online.target

[Service]
User=$username
ExecStart=sh /home/$username/tunnel-sh.sh

[Install]
WantedBy=default.target
```
注意把$username换成自己的。
####5.启动&开机自启
```
sudo systemctl start cloudflared-tunnel.service
sudo systemctl enable cloudflared-tunnel.service
```
####6.配置application
ssh是无法直接访问的，但是cloudflare提供了application套件，配置位于cloudflare的zerotrush下的access目录。
进去之后选择`add an application`，类型选`self hosted`
`Application Configuration`配置如下：
`Application name(Required)`:app名称，随意设定
`Session Duration(Required)`:会话时长，设置一次会话多久超时，一般默认24即可。
`Application domain`:我们在服务器上的配置中ssh访问链接为ssh.example.com，因此这里填`ssh`
`Domain(Required)`:域名，我们在服务器上的配置中ssh访问链接为ssh.example.com，因此这里填`example.com`
然后点击next。
`Policy`配置与安全性相关：，规则名称
`Policy name`：随意设定
`Action`:选择这个规则下的内容允许还是拒绝
`Session duration`：一次会话多久过期
`Configure rules`配置如下：
`Selector`:规则认证方式，以email为例，会要求每次进入网页验证邮箱PIN码
`Value`：规则，与`Selector`相关。
然后点击next。
在`Additional settings`下的`Browser rendering`切换为SSH，最后点击`add application`即可web访问到ssh了。


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/08/18/64df0b17828f8.png