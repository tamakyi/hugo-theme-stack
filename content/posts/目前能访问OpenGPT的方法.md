---
title: "目前能访问OpenGPT的方法"
categories: [ "记录" ]
tags: [ "opengpt","chatgpt","新必应" ]
draft: false
slug: "accessofopengpt"
date: "2023-02-11 18:29:00"
---

目前访问[ChatGPT主页][1]多半会得到以下几种结果：
####·OpenAI‘s services are not available in your country·####
如果你没挂梯子，那么请挂上梯子并打开全局代理模式，选择除香港外的服务器。
如果想使用规则模式，那么需要把以下内容添加到规则模式`DOMAIN-SUFFIX`：
```
openai.com
auth0.com
sentry.io
google.com
googleapis.com
hcaptcha.com
recaptcha.net
gstatic.com
gvt2.com
gvt1.com
gvt3.com
msftconnecttest.com
live.com
sfx.ms
microsoft.com
```
####·Access Denied·####
过于频繁的切换服务器/当前IP已经被BAN。
####·访问[新必应][2]提示404后跳转到必应中国·####
参考：[PCBeta][3]
需要安装插件[Gooreplacer][4]，请求/相应头设置如下：
```
动作类型：修改
键名：x-forwarded-for
键值：1.1.1.1
匹配模式：^http(s?)://(www\.)?bing\.com/?(.*)
匹配类型：正则表达式
```
设置页面保留所有`ON`。
再进入[新必应][5]即可看到加入队列的选项。
####·新必应加入等待列表提示“出错了，请重试”·####
官方回复：[官方社区答疑][6]
```
可能受当前地区、网络 IP、申请频繁、频繁更换 IP 来源影响导致暂时禁止访问，

您可以耐心等待一段时间或几天后再进行加入申请，看看是否可行；

如果急需体验 Microsoft 新必应相关的功能的话，或许可以尝试通过重新注册新的 Microsoft 账户来申请加入，看看是否可行。
```


  [1]: https://chat.openai.com/
  [2]: https://www.bing.com/new
  [3]: https://bbs.pcbeta.com/viewthread-1958967-1-1.html
  [4]: https://microsoftedge.microsoft.com/addons/detail/gooreplacer/cidbonnpjopamnhfjdgfcmjmlmehjnej?hl=zh-CN
  [5]: https://www.bing.com/new
  [6]: https://answers.microsoft.com/zh-hans/microsoftedge/forum/all/microsoft-%E6%96%B0%E5%BF%85%E5%BA%94/15dc9cb5-86ae-4f2c-9f27-f09f7bae901c