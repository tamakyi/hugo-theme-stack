---
title: "使用lskypro作为typecho图床"
categories: [ "记录" ]
tags: [ "lskypro","图床","typecho" ]
draft: false
slug: "lskypro"
date: "2022-07-11 20:46:00"
---

如果你的服务器在国外导致部分内容加载缓慢，同时因为CDN需要准备七七八八的(cloudflare除外，但是体验一样不好)，又信不过国内一众免费图床（稳定性和安全性问题），那么可以试着使用lskypro在国内服务器自建图床。

官网地址：[Lsky Pro V 2.x | Lsky Pro](https://docs.lsky.pro/docs/v2/#%E4%BB%8B%E7%BB%8D)

依赖：（推荐使用宝塔面板安装依赖插件，以下操作均在宝塔面板操作并通过测试）

* PHP >= 8.0.2
* BCMath PHP 扩展
* Ctype PHP 扩展
* DOM PHP 拓展
* Fileinfo PHP 扩展
* JSON PHP 扩展
* Mbstring PHP 扩展
* OpenSSL PHP 扩展
* PDO PHP 扩展
* Tokenizer PHP 扩展
* XML PHP 扩展
* Imagick 拓展
* exec、shell_exec 函数
* readlink、symlink 函数
* putenv、getenv 函数
* 关闭`open_basedir`(即“防跨站”，在宝塔的网站-设置-网站目录-防跨站攻击(open_basedir))

数据库版本要求：

* Mysql 5.7+
* PostgreSQL 9.6+
* SQLite 3.8.8+
* SQL Server 2017+

软件下载地址：[https://github.com/lsky-org/lsky-pro/releases](https://github.com/lsky-org/lsky-pro/releases)

部署到服务器上之后，设置好伪静态：

```
location / {
  try_files $uri $uri/ /index.php?$query_string;
}
```

运行目录选择`/public`。

可能因为权限问题导致目录访问不了等问题，因此要在lskypro的目录运行：

`chown -R www:www *`

`chmod -R 755 *`

之后访问安装的地址即可进入检测界面，这里可能会提示`chown`、`chmod`等功能不可用，可以在宝塔的PHP设置中把以上的禁用函数取消掉。

如无问题，即可进入账户设置和服务器链接的界面。

---

安装完成后默认是所有人都能上传的，这可能会导致一些问题，因此最好是设置一个管理员群组：

![请输入图片描述](https://pic.tama.guru/i/2022/07/11/62cc175c060a8.png)

在设置中关闭游客上传和注册功能：

![请输入图片描述](https://pic.tama.guru/i/2022/07/11/62cc17b76b256.png)

接口打开留着以便后面使用。

---

接入typecho：

[typecho兰空（LskyPro）图床插件 - Cold&#39; Blog (coldyun.cn)](https://www.coldyun.cn/archives/140.html)在莫言的1.0版本插件基础上新增了对lskypro2.0版本的支持，直接到他的博客下载附件上传到typecho下的plugin安装即可。插件需要填写`Api`和`Token`两项，其中`Token`与上面的接口有关，我们需要通过post的方式获得`token`：此处使用[Postman](https://www.postman.com/)：

注册成功后在左侧的collections点击加号，在New Collection点击`GET New Request`![请输入图片描述](https://pic.tama.guru/i/2022/07/11/62cc19d910ed0.png)

接下来在右侧把GET切换成POST：

![请输入图片描述](https://pic.tama.guru/i/2022/07/11/62cc1a8d574f8.png)

其中链接填写`https://hostname/api/v1/tokens`  hostname是你的lskypro服务安装域名。

key填写`email`和`password`后面的value为lskypro安装注册时填写的管理员邮箱和密码，之后点击Send，下方会提示：![请输入图片描述](https://pic.tama.guru/i/2022/07/11/62cc1b5f8d671.png)

此处即我们需要的token，填写进插件即可，后面就能正常使用了。


`[scode type="yellow"]注意！如果提示500错误，极有可能是跨域设置问题，对于宝塔面板需要关掉防跨站攻击，对于lnmp需要进入`lnmp/tools`目录执行`remove_open_basedir_restriction.sh`，并输入图床源代码所在的目录，保存并执行`lnmp restart即可`[/scode]`
