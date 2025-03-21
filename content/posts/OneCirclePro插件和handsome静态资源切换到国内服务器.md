---
title: "OneCirclePro插件和handsome静态资源切换到国内服务器"
categories: [ "记录" ]
tags: [ "OneCirclePro","静态资源" ]
draft: false
slug: "OneCirclePro-jscss"
date: "2022-07-11 22:52:00"
---

体验了几天这个主题，发现资源加载是真的离谱，动不动10s+：

![请输入图片描述](https://pic.tama.guru/i/2022/07/11/62cc370ea8a47.png)

于是就想着把部分静态资源扔国内来了。

直接把包内`themeCore\themes\default\statics`打包上传到国内服务器搭建的网站目录下，最好和其他资源间隔开来，比如`https://hostname/cdn/OneCircle/`，然后修改`themeCore/themes/default/apps/main/content.phtml`和`themeCore/themes/default/apps/cpanel/content_nosidebar.phtml`把`{%config theme_url%}`替换成`https://hostname/cdn/OneCircle/`，再刷新即可。

`[scode type="yellow"]注意！部分资源位于`themeCore\themes\default\apps\cpanel\statics`下，此部分资源和上面的不一样，也需要提取到国内服务器上并进行替换。（说的就是你！`themeCore/themes/default/apps/cpanel/content_nosidebar.phtml` 你mua的！）[/scode]`

对于部分资源会得到`has been blocked by CORS policy: No 'Access-Control-Allow-Origin'`的跨域警告，是因为

```
<link rel="stylesheet" href="https://hostname/cdn/OneCircle/statics/scss/master.styles.min.css?v={%config version%}">
```

这里的`master.styles.min.js`会请求几个woff文件，初步解决方案是只把这里修改回去，即：

```
<link rel="stylesheet" href="{%config theme_url%}/statics/scss/master.styles.min.css?v={%config version%}">
```

后面有好方法再说。。。

PS:解决方案：如果使用宝塔面板，在目标服务器（国内服务器）要关闭`防跨站攻击(open_basedir)`功能，并在配置文件的location添加以下两句即可：

```
    location / {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
      }
```

---

handsome主题内置了自定义cdn加速的功能，在主题设置的`速度优化-主题本地静态资源自定义cdn加速`下，填写加速地址：

> * 你需要把asset目录上传到你的cdn服务器上，比如CDN服务器的 handsome目录里，地址即为 `https://cdn.ihewro.com/handsome/assets/`
> * 在当前框中就填入该地址，主题就会引用你搭建的cdn上面的资源，而不再引用当前服务器上的资源
