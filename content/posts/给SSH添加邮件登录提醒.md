---
title: "给SSH添加邮件登录提醒"
categories: [ "记录" ]
tags: [ "ssh","mail","邮件提醒","msmtp","mutt" ]
draft: false
slug: "ssh_login_mail"
date: "2023-12-26 22:09:04"
---

影响SSH登录安全性的因素不外乎：禁用密码登录、密钥强度、修改默认端口、仅指定IP登录，但是如果每次登录的时候能够及时的发一封邮件提醒，可以更好的管控服务器登录状态，也能更安心些。


<!--more-->

需要用到`msmtp`和`mutt`两个软件。
#### 安装所需软件：
对于Centos：
```yum install msmtp mutt```
对于debian系：
```apt install msmtp mutt```
对于arch系用户：
```pacman -S msmtp mutt```

#### 配置msmtp
其实msmtp就是一个支持服务器配置文件的轻型SMTP客户端，配合mutt可以达到邮件发送的效果。
对于debian系统，可以在
```/usr/share/doc/msmtp/examples/```
下找到默认配置文件。其中`msmtprc-system.example`代表全局配置，`msmtprc-user.example`代表用户配置。如果需要所有用户都使用这个配置，则可以执行
```cat /usr/share/doc/msmtp/examples/ > /etc/msmtprc```
然后修改`/etc/msmtprc`文件：
*注意，实际使用时不要吧中文注释也复制进去*
```
# Example for a system wide configuration file

# A system wide configuration file is optional.
# If it exists, it usually defines a default account.
# This allows msmtp to be used like /usr/sbin/sendmail.
account default

# The SMTP smarthost
host smtp.qq.com #SMTP域名，例如QQ邮箱为smtp.qq.com

# Use TLS on port 465
port 465 #465为默认smtp tls端口，如果选择不加密一般改成25
tls on #是否开启TLS
tls_starttls off # STARTTLS是对纯文本通讯协议的扩展。它提供一种方式将纯文本链接升级为加密链接（TLS或SSL），而不是另外使用一个端口做加密通讯。我在使用过程中发现开启时大概率发送失败，可能和qq的smtp服务器有关，因此保持默认关闭即可。

# Construct envelope-from addresses of the form "user@oursite.example"
from xxx@qq.com #发送邮件的地址，以qq邮箱为例
auth on #启用验证
user xxx@qq.com #用户名，以qq邮箱为例
password xxxxxxxx #这里填授权码或密码

# Syslog logging with facility LOG_MAIL instead of the default LOG_USER
syslog LOG_MAIL
```
保存文件后可以执行
```
echo "this is a test"|msmtp -v 收件邮箱@qq.com
```
然后会看到服务器执行邮件发送的操作：
```
...
<-- 220 smtp.qq.com MX Tencent Server
--> EHLO localhost
<-- 250-smtp.qq.com
<-- 250-8BITMIME
<-- 250-AUTH=PLAIN LOGIN XALIOAUTH
<-- 250-AUTH PLAIN LOGIN XALIOAUTH
<-- 250-PIPELINING
<-- 250 DSN
--> AUTH PLAIN *******************************
<-- 235 Authentication successful
--> MAIL FROM:<xxx@qq.com>
--> RCPT TO:<收件邮箱@qq.com>
--> DATA
<-- 250 Mail Ok
<-- 250 Rcpt Ok
<-- 354 End data with <CR><LF>.<CR><LF>
--> From: xxx@qq.com
--> Date: Tue, 26 Dec 2023 21:10:21 +0800
--> test infomation
--> .
<-- 250 Data Ok: queued as freedom
--> QUIT
<-- 221 Bye
```
得到类似以上的信息，可以判定配置配置无误了。如果收件箱并没有看到邮件，需要到寄件邮箱查询是否被当成垃圾邮件拒收了。当然这并不影响后续使用，因为这本身就是一封只有英文标题无任何内容的邮件，很容易被ban掉。
![被拦截了][1]

最后，我们还需要修改`/etc/Muttrc`，在最后面加上：
```
set sendmail="/usr/bin/msmtp"
set use_from=yes
set realname="你想要显示的发送名称"
set from=xxx@qq.com
```

#### 生成配置文件
在`/etc/ssh`下生成并编辑`ssh_alarm.sh`
```
nano /etc/ssh/ssh_alarm.sh
```
文件内容如下：
```
#!/bin/bash

if [ -z "${SSH_CONNECTION}" ];then
    echo '$SSH_CONNECTION:not exists'
    exit 1
fi

#定义变量
from_ip=`echo ${SSH_CONNECTION} | awk '{print$1}'`
from_port=`echo ${SSH_CONNECTION} | awk '{print$2}'`
server_ip=`echo ${SSH_CONNECTION} | awk '{print$3}'`
user=`echo ${LOGNAME}`
hostname_1=`echo ${HOSTNAME} "(" $server_ip ")"`
server_time=`echo $(date +"%Y-%m-%d %R")`
e_mail=收件地址@qq.com

#IP白名单，192.168.1.1这个IP登录时不提示
ip_exit=`echo ${from_ip}|cut -d "." -f 1,2,3`
if [ "${ip_exit}" == "192.168.1.1" ];then
  exit 1
fi

#用户登录白名单，username这个用户登录时不提示
if [ "${user}" == "username" ];then
  exit 1
fi

#Send mail
echo -e "
**********SSH登录警告**********\n

    登录主机:${hostname_1}\n
    登录用户:${user}\n
    登录时间:${server_time}\n
    登录IP:${from_ip}\n
    登录IP端口:${from_port}\n
    请注意核查是否可信登录，如非可信，请立即更换密匙和密码。

******************************"|mutt -s "服务器SSH登录警告" ${e_mail}
```
保存后执行：
```
bash /etc/ssh/ssh_alarm.sh
```
执行完毕不会有任何回显，但是收件端`收件地址@qq.com`应该会收到邮件：
![成功收到邮件][2]
最后需要配置一个sshrc文件，这个文件的作用是当连接ssh时自动执行文件内的脚本：
```
nano /etc/ssh/sshrc
```
文件内容如下：
```                                                                                                                 
bash /etc/ssh/ssh_alarm.sh
```
保存之后再次登录ssh服务器，就会自动执行`ssh_alarm.sh`脚本发送邮件了。


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/12/26/658adb8d00c1a.png
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/12/26/658addbc2a019.png