---
title: "在静态网站上使用waline评论系统"
categories: [ "记录" ]
tags: [ "waline","valine","静态网站","评论系统","serverless" ]
draft: false
slug: "waline-on-html"
date: "2024-03-10 01:24:00"
---

#### Waline是什么
>一款从 Valine 衍生的带后端评论系统。可以将 Waline 等价成 With backend Valine.

简单来说，Waline可以引入到任意网站上，比如hugo等搭建的静态博客系统。


<!--more-->


#### 为什么要用Waline
看到这个的第一眼我就心想：卧槽，我也要整一个！
<center>![文章反应功能][1]</center>
于是现在已经加入到提问箱首页了：[狼的提问箱][2]。

#### 怎么搭建呢？
直接参考[官网][3]即可，文档较为完善。推荐使用vercel+LeanCloud，可以实现无服务器搭建，当然访问速度有一些慢。

#### 需要注意什么？
Waline主要分为三个部分进行配置：

##### vercel环境变量
配置地址:https://vercel.com/%username%s-projects/%project_name%/settings/environment-variables
配置文档:[https://waline.js.org/reference/server/env.html][4]
描述：一些全局性的配置
具体可配置项目如下：
|          环境变量名称         | 默认值 |           备注                               |
|:-----------------------------:|:--------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------:|
|            LEAN_ID            |                          -                         |                                                                LeanCloud 应用的 App ID                                                                |
|            LEAN_KEY           |                          -                         |                                                                LeanCloud 应用的 App Key                                                               |
|        LEAN_MASTER_KEY        |                          -                         |                                                      LeanCloud 应用的 Master Key 用于后台修改数据                                                     |
|          LEAN_SERVER          |                          -                         |                                                       LeanCloud 服务地址，国内版用户需要配置此项                                                      |
|           SITE_NAME           |                                                    |                                                                        博客名称                                                                       |
|            SITE_URL           |                                                    |                                                                        博客地址                                                                       |
|             LOGIN             |                                                    |                                                       当设置为 LOGIN=force 时会要求登录才能评论                                                       |
|       DISABLE_USERAGENT       |                        false                       |                                                             是否隐藏评论者的 UA，默认为否                                                             |
|         DISABLE_REGION        |                        false                       |                                                                 是否隐藏评论者的归属地                                                                |
|     DISABLE_AUTHOR_NOTIFY     |                        false                       |                                                                   是否禁止新评论通知                                                                  |
|          AVATAR_PROXY         |          https://avatar.75cdn.workers.dev          |                                                          头像的代理地址，设置 false 关闭代理                                                          |
|          GRAVATAR_STR         | https://seccdn.libravatar.org/avatar/{{mail\md5}} |                                                        Gravatar 头像的地址，基于 nunjucks 语法                                                        |
|             LEVELS            |                                                    |                                                        设置后会根据评论数为每个用户提供等级标签                                                       |
|             IPQPS             |                         60                         |                                                 基于 IP 的评论发布频率限制，单位为秒。设置为 0 不限制                                                 |
|         SECURE_DOMAINS        |                                                    |                                                         安全域名配置，支持逗号分隔配置多个域名                                                        |
|          AKISMET_KEY          |                    70542d86693e                    |                                               Akismet 反垃圾评论服务 Key (默认开启，不用请设置为 false)                                               |
|         COMMENT_AUDIT         |                        false                       |                              评论发布审核开关。开启后评论需要经过管理员审核后才能显示，所以建议在评论框默认文字上提供提示                             |
|        RECAPTCHA_V3_KEY       |                                                    |                                                          reCAPTCHA V3 key，须与客户端同时配置                                                         |
|      RECAPTCHA_V3_SECRET      |                                                    |                                                       reCAPTCHA V3 secret，服务端使用，不可泄漏                                                       |
|         TURNSTILE_KEY         |                                                    |                                                           Turnstile key，须与客户端同时配置                                                           |
|        TURNSTILE_SECRET       |                                                    |                                                         Turnstile secret，服务端使用，不可泄漏                                                        |
|        MARKDOWN_CONFIG        |                         {}                         |                                                                    MarkdownIt 配置                                                                    |
|       MARKDOWN_HIGHLIGHT      |                        true                        |                                                                      是否启用高亮                                                                     |
|         MARKDOWN_EMOJI        |                        true                        |                                                                是否启用 Emoji 缩写支持                                                                |
|          MARKDOWN_SUB         |                        true                        |                                                                   是否启用下角标支持                                                                  |
|          MARKDOWN_SUP         |                        true                        |                                                                   是否启用上角标支持                                                                  |
|          MARKDOWN_TEX         |                       mathjax                      |                                                      解析 TeX 的服务，支持 mathjax、katex、false                                                      |
|        MARKDOWN_MATHJAX       |                         {}                         |                                                                      MathJax 选项                                                                     |
|         MARKDOWN_KATEX        |                         {}                         |                                                                       KaTeX 选项                                                                      |
|          SMTP_SERVICE         |                                                    |                                                                SMTP 邮件发送服务提供商                                                                |
|           SMTP_HOST           |                                                    |                                                                    SMTP 服务器地址                                                                    |
|           SMTP_PORT           |                                                    |                                                                   SMTP 服务器端口。                                                                   |
|           SMTP_USER           |                                                    |                                                                      SMTP 用户名                                                                      |
|           SMTP_PASS           |                                                    |                                                                       SMTP 密码                                                                       |
|          SMTP_SECURE          |                                                    |                                                                 是否使用 SSL 连接 SMTP                                                                |
|          SENDER_NAME          |                                                    |                                                                 自定义发送邮件的发件人                                                                |
|          SENDER_EMAIL         |                                                    |                                                                自定义发送邮件的发件地址                                                               |
|            MONGO_DB           |                         　                         |                                                                   MongoDB 数据库名称                                                                  |
|           MONGO_USER          |                         　                         |                                                                  MongoDB 服务的用户名                                                                 |
|         MONGO_PASSWORD        |                         　                         |                                                                   MongoDB 服务的密码                                                                  |
|           MONGO_HOST          |                      127.0.0.1                     |                                                           MongoDB   服务的地址，支持数组格式                                                          |
|           MONGO_PORT          |                        27017                       |                                                           MongoDB   服务的端口，支持数组格式                                                          |
|        MONGO_REPLICASET       |                         　                         |                                                                      MongoDB 集群                                                                     |
|        MONGO_AUTHSOURCE       |                         　                         |                                                                     MongoDB 认证源                                                                    |
|         MONGO_OPT_SSL         |                        FALSE                       |                                                                 是否使用 SSL 进行连接                                                                 |
|            MYSQL_DB           |                         　                         |                                                                    MySQL 数据库库名                                                                   |
|           MYSQL_USER          |                         　                         |                                                                  MySQL 数据库的用户名                                                                 |
|         MYSQL_PASSWORD        |                         　                         |                                                                   MySQL 数据库的密码                                                                  |
|           MYSQL_HOST          |                      127.0.0.1                     |                                                                    MySQL 服务的地址                                                                   |
|           MYSQL_PORT          |                        3306                        |                                                                    MySQL 服务的端口                                                                   |
|          MYSQL_PREFIX         |                         wl_                        |                                                                  MySQL 数据表的表前缀                                                                 |
|         MYSQL_CHARSET         |                       utf8mb4                      |                                                                  MySQL 数据表的字符集                                                                 |
|           MYSQL_SSL           |                        FALSE                       |                                                            是否使用 SSL MYSQL   连接数据库                                                            |
|            TIDB_DB            |                         　                         |                                                                    TiDB 数据库库名                                                                    |
|           TIDB_USER           |                         　                         |                                                                  TiDB 数据库的用户名                                                                  |
|         TIDB_PASSWORD         |                         　                         |                                                                   TiDB 数据库的密码                                                                   |
|           TIDB_HOST           |                      127.0.0.1                     |                                                                    TiDB 服务的地址                                                                    |
|           TIDB_PORT           |                        4000                        |                                                                    TiDB 服务的端口                                                                    |
|          TIDB_PREFIX          |                         wl_                        |                                                                  TiDB 数据表的表前缀                                                                  |
|          TIDB_CHARSET         |                       utf8mb4                      |                                                                  TiDB 数据表的字符集                                                                  |
|          SQLITE_PATH          |                         　                         |                                                    SQLite 数据库文件的路径，该路径不包含文件名本身                                                    |
|           JWT_TOKEN           |                         　                         |                                                              用户登录密钥，随机字符串即可                                                             |
|           SQLITE_DB           |                       waline                       |                                                  SQLite   数据库文件名，若文件名变化需要修改该字段值                                                  |
|         SQLITE_PREFIX         |                         wl_                        |                                                                 SQLite 数据表的表前缀                                                                 |
|             PG_DB             |                         　                         |                                                                 PostgreSQL 数据库库名                                                                 |
|            PG_USER            |                         　                         |                                                              PostgreSQL   数据库的用户名                                                              |
|          PG_PASSWORD          |                         　                         |                                                               PostgreSQL   数据库的密码                                                               |
|            PG_HOST            |                      127.0.0.1                     |                                                                 PostgreSQL 服务的地址                                                                 |
|            PG_PORT            |                        3211                        |                                                                 PostgreSQL 服务的端口                                                                 |
|           PG_PREFIX           |                         wl_                        |                                                              PostgreSQL   数据表的表前缀                                                              |
|             PG_SSL            |                        FALSE                       |                                                         是否使用 SSL 连接   PostgreSQL 数据库                                                         |
|       POSTGRES_DATABASE       |                         　                         |                                                                        同 PG_DB                                                                       |
|         POSTGRES_USER         |                         　                         |                                                                       同 PG_USER                                                                      |
|       POSTGRES_PASSWORD       |                         　                         |                                                                     同 PG_PASSWORD                                                                    |
|         POSTGRES_HOST         |                      127.0.0.1                     |                                                                       同 PG_HOST                                                                      |
|         POSTGRES_PORT         |                        3211                        |                                                                       同 PG_PORT                                                                      |
|        POSTGRES_PREFIX        |                         wl_                        |                                                                      同 PG_PREFIX                                                                     |
|          POSTGRES_SSL         |                        FALSE                       |                                                                    同 POSTGRES_SSL                                                                    |
|            TCB_ENV            |                         　                         |                                                                   腾讯云开发环境 ID                                                                   |
|             TCB_ID            |                         　                         |                                                                   腾讯云 API 密钥 ID                                                                  |
|            TCB_KEY            |                         　                         |                                                                  腾讯云 API 密钥 Key                                                                  |
|           JWT_TOKEN           |                         　                         |                                         用户登录密钥，如果没有配任何环境变量的话需要配置此变量，随机字符串即可                                        |
|          GITHUB_TOKEN         |                         　                         |                                                       Personal   access tokensopen in new window                                                      |
|          GITHUB_REPO          |                         　                         |                                                             仓库名称，例如 walinejs/waline                                                            |
|          GITHUB_PATH          |                         　                         |                                          数据存储目录，例如 data 表示存储在 data 目录下，默认存在仓库根目录下                                         |
|        DETA_PROJECT_KEY       |                         　                         |                                                                     Deta 项目密钥                                                                     |
|           OAUTH_URL           |               https://oauth.lithub.cc              | OAuth   第三方登录服务地址，可以 自建 authopen in new window 这是能让用户使用   GitHub, Twitter, Facebook, Google, 微博等第三方账户登录最简单的方式。 |
|            WEBHOOK            |                         　                         |                                                 评论成功后会向 WEBHOOK   配置的地址发送一条 POST 请求                                                 |
| WALINE_ADMIN_MODULE_ASSET_URL |              //unpkg.com/@waline/admin             |                                                                   Waline admin 地址                                                                   |
|          IP2REGION_DB         |                         　                         |                                                                  自定义 IP 查询库路径                                                                 |

##### index.js（服务端）配置
配置地址:https://github.com/%username%/%repositorie%/blob/main/index.js
配置文档:[https://waline.js.org/reference/server/config.html][5]
描述：该文件位于github私有仓库内，其中一些项目与vercel环境变量效果一致。在这里修改过的文件会自动在vercel内重新build。
可配置的项目：
|        名称       |                   参考                  |                                     说明                                     |
|:-----------------:|:---------------------------------------:|:----------------------------------------------------------------------------:|
|      plugins      |       plugins: [HelloWorldPlugin],      |                                   插件系统                                   |
|   secureDomains   |     secureDomains: 'waline.js.org',     |                                   安全域名                                   |
|   forbiddenWords  | forbiddenWords: ['违禁词A', '违禁词B'], |                                    违禁词                                    |
|   disallowIPList  | disallowIPList: ['8.8.8.8', '4.4.4.4'], |                                   IP 黑名单                                  |
|    mailSubject    |                                         |              评论回复邮件标题自定义，等同于环境变量 MAIL_SUBJECT             |
|    mailTemplate   |                                         |             评论回复邮件内容自定义，等同于环境变量 MAIL_TEMPLATE             |
|  mailSubjectAdmin |                                         |          新评论通知邮件标题自定义，等同于环境变量 MAIL_SUBJECT_ADMIN         |
| mailTemplateAdmin |                                         |         新评论通知邮件内容自定义，等同于环境变量 MAIL_TEMPLATE_ADMIN         |
|     QQTemplate    |                                         |                 QQ   评论通知模板，等同于环境变量 QQ_TEMPLATE                |
|     TGTemplate    |                                         |              Telegram   评论通知模板，等同于环境变量 TG_TEMPLATE             |
|     SCTemplate    |                                         |              Server酱   评论通知模板，等同于环境变量 SC_TEMPLATE             |
|       model       |                                         | 自定义数据库服务（见https://waline.js.org/cookbook/customize/database.html） |
|  encryptPassword  |                                         |   自定义用户系统（见https://waline.js.org/cookbook/customize/userdb.html）   |
|     评论 Hooks    |                                         |                       包含一些自定义HOOK，详情见文档。                       |
修改过的index.js文件参考如下：
```javascript
const Application = require('@waline/vercel');

module.exports = Application({
  plugins: [],
  //secureDomains: '*.tama.guru',
  COMMENT_AUDIT: true,
  forbiddenWords: ['***', '***'],
});
```

##### 前端(html内)配置
配置地址:https://%yoursite%/%sitename%.html
配置文档:[https://waline.netlify.app/client/basic.html][6]
描述：每个html都可以独立配置以达到不同的显示效果
**注意：以下配置针对v2版本生效，v3未知**
可配置的项目：
|     名称     |            类型            |                                                                           说明                                                                          |
|:------------:|:--------------------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------:|
|      el      |           string           |                                                   Waline 的初始化挂载器。必须是一个有效的 CSS 选择器。                                                  |
|   serverURL  |           string           |                                                                  Waline 的服务端地址。                                                                  |
|   wordLimit  | number \ [number, number] |                                             评论字数限制。填入单个数字时为最大字数限制。设置为 0 时无限制。                                             |
|     path     |           string           | 当前 文章页 路径，用于区分不同的 文章页，以保证正确读取该 文章页   下的评论列表。默认为`window.location.pathname`，可选为`window.location.href`或其他。 |
|    avatar    |           string           |                                                             Gravatar 头像展示方式。见文档。                                                             |
|     meta     |          string[]          |                                                      评论者相关属性。可选值: 'nick', 'mail', 'link'                                                     |
|   pageSize   |           number           |                                                                 评论列表分页，每页条数。                                                                |
|     lang     |           string           |                                                              多语言支持。可自定义，见文档。                                                             |
|    visitor   |           boolean          |                                                                   开启文章访问量统计。                                                                  |
|     dark     |           string           |                                                                暗黑模式适配。详情见文档。                                                               |
|   highlight  |           boolean          |                                                                   代码高亮，默认开启。                                                                  |
|   avatarCDN  |           string           |                                           设置 Gravatar 头像 CDN 地址。默认值是https://sdn.geekzu.org/avatar/                                           |
|  avatarForce |           boolean          |                                                          每次访问是否强制拉取最新的评论列表头像                                                         |
|     emoji    |   (string \ EmojiInfo)[]  |                                                                   表情设置，详见文档。                                                                  |
| requiredMeta |          string[]          |                                                       设置必填项，默认匿名，可填['nick', 'mail']等                                                      |
|  uploadImage |          Function          |                                           自定义图片上传方法，方便更好的存储图片。方法执行时会将图片对象传入。                                          |
|     login    |           string           |        登录模式状态，分为'enable': 启用登录 (默认) 'disable': 禁用登录，用户只能填写信息评论 'force':   强制登录，用户必须注册并登录才可发布评论        |
|   copyright  |           boolean          |                                                                  是否显示页脚版权信息。                                                                 |
修改过的html文件参考如下：
```html
<script src="/waline.js"></script>
<link href='/waline.css' rel='stylesheet' />
 <div id="waline"></div>
 <script>
     const waline = Waline.init({
         el: '#waline',
         serverURL: 'https://%yourwalineserversite%',
         reaction: ture,
         meta: ['nick','mail'],
         requiredMeta: ['mail'],
         pageview: true,
         search: false,
         comment: false,
         login: 'disable',
         wordLimit: 50,
         pageSize: 5,
         avatar: 'monsterid',
         emoji: [
             'https://unpkg.com/@waline/emojis@1.2.0/tieba',
         ],
         //禁止图片上传按钮
         imageUploader: false,
         //不显示版权标志
         copyright: false,
     });
 </script>
```
**再次提醒，我引用最新的v3版本会报错，建议用v2。**

  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/03/10/65ec8d91d5b8a.png
  [2]: https://box.tama.guru/
  [3]: https://waline.js.org/
  [4]: https://waline.js.org/reference/server/env.html
  [5]: https://waline.js.org/reference/server/config.html
  [6]: https://waline.netlify.app/client/basic.html