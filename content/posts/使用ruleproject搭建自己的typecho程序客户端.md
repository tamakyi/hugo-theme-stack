---
title: "使用ruleproject搭建自己的typecho程序客户端"
categories: [ "记录" ]
tags: [ "typecho","ruleproject","typecho客户端","html5","java","宣传图" ]
draft: false
slug: "ruleproject"
date: "2023-11-17 23:12:00"
---

卧槽牛皮，这东西牛啊！通过这个教程已经成功弄出了安卓端apk并正常使用！
本站安卓客户端：[https://share.tama.guru/d/App/Android/latest_release.apk][1]
</br>本站自签证iOS端：[https://share.tama.guru/d/App/iOS/%E7%8B%BC%E7%9A%84%E5%85%AC%E5%91%8A%E6%9D%BF.mobileconfig][2]

### 介绍

RuleApi，基于typecho正式版数据库，使用JAVA语言Springboot框架，整合redis缓存数据库，COS、OSS对象存储，是目前typecho程序功能最全，接口最完善，用户体验最好，且性能最佳的API程序。集成了用户模块（登陆，注册，邮箱验证，用户查询，用户修改），文章模块，评论模块，分类模块，和上传模块（三合一上传方式，OSS，COS，本地上传均可），在安装完成后，可以进一步扩展typecho网站的功能，并实现更强大的性能，更全面的应用范围。

### 截图
![安卓端安装][3]
![内容][4]

### 搭建教程
以下均为安卓端app的配置，ios端个人能力有限不使用。
#### API端
基本上可以参考官方项目的说明，官方提供centos使用的[一键脚本][5]，因为我用的debian，用的是手动安装。以下操作也是基础手动安装。
#### 安装依赖环境

首先是JAVA，官方建议安装8u311版本，可以去网上找，也可以使用我下载下来的[文件][6]。下载之后解压到`/opt`并命名为`jdk1.8`，整个路径看起来应该像：
 ```
# ls /opt/jdk1.8/ bin  COPYRIGHT	include  javafx-src.zip  jmc.txt  jre  legal  lib  LICENSE man  README.html  release src.zip	THIRDPARTYLICENSEREADME-JAVAFX.txt  THIRDPARTYLICENSEREADME.txt
```
然后配置环境，修改`/etc/profile`，添加下面的语句到最后：
```
export JAVA_HOME=/opt/jdk1.8
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin
```
并执行`source /etc/profile`使其生效。
其他还需要安装Redis、Mysql和Nginx，如果安装过宝塔的直接在面板上安装，基本上没有版本限制所以此处不写过程了。
最后还需要安装`screen`用于维持ruleapi后台运行。
#### 下载所需文件
在`/opt`新建一个文件夹，比如`ruleapi`，把以下文件都下载下来。
```
	wget -c http://shell.ruletree.club/RuleApi.jar
	wget -c http://shell.ruletree.club/application.properties
	wget -c http://shell.ruletree.club/apiResult.php
```
整个路径看起来应该像：
```
#ls /opt/ruleapi
apiResult.php  application.properties RuleApi.jar
然后需要把`apiResult.php`复制到typecho的根目录。
相关文件也已经上传云盘:[https://share.tama.guru/%E8%BF%9C%E7%A8%8B%E4%B8%8B%E8%BD%BD%EF%BC%88%E4%BB%93%E5%BA%93%EF%BC%89/ruleproject][7]
```
#### 修改ruleapi配置
修改`application.properties`，主要修改以下几项：
```
server.port=8080   #ruleapi运行的端口，如果8080被占用了就手动改。

spring.datasource.url=jdbc:mysql://127.0.0.1:3306/<typecho数据库名>?characterEncoding=UTF-8
spring.datasource.username=<typecho数据库用户名>
spring.datasource.password=<typecho数据库密码>
mybatis.configuration.variables.prefix=<typecho数据表前缀>

spring.redis.password=<redis密码，默认为空，如果配置了其他密码自己手动修改>

spring.mail.host=<smtp服务器>
spring.mail.username=<smtp登录名>
#spring.mail.from不支持中文，需要中文，看mail.properties
spring.mail.password=<smtp密码>
spring.mail.properties.mail.smtp.port:<smtp登录端口>
spring.mail.properties.mail.smtp.ssl.enable: true <smtp是否需要ssl>
spring.mail.default-encoding=UTF-8 <编码格式>

webinfo.key=<登录网页控制台的key>
```
#### 运行程序并保持在后台运行
```
#screen -S ruleapi
#java -jar RuleApi.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::       (v2.2.11.RELEASE)

2023-11-17 18:42:00  INFO 966545 --- [           main] com.RuleApi.Application                  55 : Starting Application v1.3.4 on xxxxxxxxx with PID 966545 /opt/ruleapi/RuleApi.jar started by root in /opt/ruleapi
2023-11-17 18:42:00  INFO 966545 --- [           main] com.RuleApi.Application                  651 : No active profile set, falling back to default profiles: default
2023-11-17 18:42:01  INFO 966545 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate 249 : Multiple Spring Data modules found, entering strict repository configuration mode!
2023-11-17 18:42:01  INFO 966545 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate 127 : Bootstrapping Spring Data Redis repositories in DEFAULT mode.
2023-11-17 18:42:01  INFO 966545 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate 187 : Finished Spring Data repository scanning in 43ms. Found 0 Redis repository interfaces.
2023-11-17 18:42:03  INFO 966545 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  91 : Tomcat initialized with port(s): yourport (http)
2023-11-17 18:42:03  INFO 966545 --- [           main] o.a.coyote.http11.Http11NioProtocol      173 : Initializing ProtocolHandler ["http-nio-yourport"]
2023-11-17 18:42:03  INFO 966545 --- [           main] o.apache.catalina.core.StandardService   173 : Starting service [Tomcat]
2023-11-17 18:42:03  INFO 966545 --- [           main] org.apache.catalina.core.StandardEngine  173 : Starting Servlet engine: [Apache Tomcat/9.0.58]
2023-11-17 18:42:03  INFO 966545 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       173 : Initializing Spring embedded WebApplicationContext
2023-11-17 18:42:03  INFO 966545 --- [           main] w.s.c.ServletWebServerApplicationContext 283 : Root WebApplicationContext: initialization completed in 2660 ms
2023-11-17 18:42:05  INFO 966545 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  181 : Initializing ExecutorService 'applicationTaskExecutor'
2023-11-17 18:42:05  INFO 966545 --- [           main] o.s.b.a.w.s.WelcomePageHandlerMapping    54 : Adding welcome page: class path resource [static/index.html]
2023-11-17 18:42:05  INFO 966545 --- [           main] o.a.coyote.http11.Http11NioProtocol      173 : Starting ProtocolHandler ["http-nio-yourport"]
2023-11-17 18:42:05  INFO 966545 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  203 : Tomcat started on port(s): yourport(http) with context path ''
2023-11-17 18:42:05  INFO 966545 --- [           main] com.RuleApi.Application                  61 : Started Application in 6.022 seconds (JVM running for 6.495)
2023-11-17 18:42:14  INFO 966545 --- [nio-yourport-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       173 : Initializing Spring DispatcherServlet 'dispatcherServlet'
2023-11-17 18:42:14  INFO 966545 --- [nio-yourport-exec-1] o.s.web.servlet.DispatcherServlet        525 : Initializing Servlet 'dispatcherServlet'
2023-11-17 18:42:14  INFO 966545 --- [nio-yourport-exec-1] o.s.web.servlet.DispatcherServlet        547 : Completed initialization in 8 ms
2023-11-17 18:42:15  INFO 966545 --- [nio-yourport-exec-8] io.lettuce.core.EpollProvider            68 : Starting without optional epoll library

```
如果有上述提示则表示程序正常启动了，可以按`Ctrl+A D`保持后台运行。
#### 配置二级域名
因为使用IP+端口访问并不安全，需要配置一个二级域名用以访问。比如二级域名为https://rule.tama.host，生成并解析后需要添加伪静态规则：
```
location ^~ /images/ {
        alias /opt/ruleapi/static/;
        try_files $uri $uri/ =404;
    }
location ^~ / {
    add_header 'Access-Control-Allow-Origin' '*';
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT, DELETE';
    add_header 'Access-Control-Allow-Credentials' 'true';
    add_header 'Access-Control-Allow-Headers' 'Authorization,Content-Type,Accept,Origin,User-Agent,DNT,Cache-Control,X-Mx-ReqToken,X-Data-Type,X-Requested-With,X-Data-Type,X-Auth-Token';
    if ( $request_method = 'OPTIONS' ) { 
        return 200;
    }
     proxy_pass http://127.0.0.1:<你的ruleapi端口>;
     proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header REMOTE-HOST $remote_addr;
}
```
注意修改端口。之后访问https://rule.tama.host可以进入可视化管理页面。
![可视化界面][8]
首次进去先点击安装及更新，每次更新之后都要点这个。此处需要填写`webinfo.key`中配置的key。更新完成后关闭界面可以进入管理中心。
![安装及更新][9]
#### 管理中心配置
这里的参数很多，我的修改只为了保证程序的运行，其他细项可以自行修改。
`安全设置`建议打开机器人限制模式。
`应用模块-应用管理`是重要的步骤，内容可以按自己的设置添加修改：
![应用设置1][10]![应用设置2][11]
之后会得到一个`应用key`。
![应用key][12]
之后还需要到存储模块设置一个上传文件/视频的方式，可以选择OSS/COS/FTP/本地上传。如果使用本地上传，需要把本地&FTP图片访问地址修改为：`https://rule.tama.host/images/`，rule.tama.host修改成你ruleapi的地址。（来自：[https://www.ijkxs.com/9955.html][13]）
![本地访问图片][14]
至此api端算是配置完成了。

### APP端

#### 注册和配置dcloud
先需要注册一个[dcloud账号][15]并登录，绑定好手机号。然后到dcloud下载[HBulder X][16]。

#### 下载源码到本地并导入HBulderX
Gitee:[https://gitee.com/ruletree/RuleApp][17]
Github:[https://github.com/buxia97/RuleApp][18]
假设下载的目录是C:\Files\RuleApp，打开HBulderX，并执行`文件>导入>从本地目录导入`
![导入项目][19]

#### 修改配置项目
首先是`utils\api.js`，这里主要填写对接到ruleapi的数据，主要修改以下内容：
```
var API_URL = '<ruleapi网址>';
var appKey = "<应用key>";
//web网站地址

var GroupUrl = '<qq群链接>';
var GithubUrl = '<gayhub链接>';
```
其他可以根据注释修改。
然后修改`manifest.json`，这里是图形界面的，可以根据提示直接修改，需要注意：
1.uni-app需要重新获取，这里会提示登录dcloud账号。
2.应用版本号和应用版本名称应高于前一次配置的值，首次配置可以写1。
3.app模块配置中根据需要选择Oauth如果没有配置的话把勾取消掉。
如果不选择Push的话，每次启动app都会有弹窗提示，建议配置，配置需要到[https://dev.dcloud.net.cn/pages/app/detail][20]中`我的应用>
应用名称>Android云端证书`中查看证书详情，并记录sha1 sha256和md5值。
![证书指纹][21]
然后到`各平台信息`中填写对应值，包名也要一并填写。
然后把数值填写到`uniPush>1.0>应用信息`选择APPID，填写应用名称和包名、选择平台并选择Android应用签名。
![应用信息配置][22]
保存成功后进入`uniPush>1.0>消息推送>配置管理>应用配置`下，把Appid Appkey MasterSectrt值填写到ruleapi的`通知模块>UniPush配置`中。
![应用配置][23]
![UniPushRuleapi端][24]

#### 调试
到HBulderX的运行中选择运行到浏览器，等几分钟会跳转到浏览器并打开页面，可以查看是否有误。
![调试][25]

#### 编译
执行HBulderX的的发行>原生App-云打包。
![发行][26]
填写包名并选择使用云端证书，点击打包，会提示下载相关依赖，下载完成后会自动启动打包，如果没有配置错误一般三到五分钟后就能生成apk包。
![打包][27]
```
[HBuilder] 23:02:08.640 项目 'RuleApp-master' 开始编译...
[HBuilder] 23:02:10.336 3.96
[HBuilder] 23:02:10.336 正在编译中...
[HBuilder] 23:02:10.337 ​已开启 uni统计1.0 版本​
[HBuilder] 23:02:40.332 项目 'RuleApp-master' 编译成功。
[HBuilder] 23:02:51.222 打包成功后会自动返回下载链接。
[HBuilder] 23:02:51.222 打包过程查询请点菜单发行-查看云打包状态。
[HBuilder] 23:02:51.222 周五傍晚等高峰期打包排队较长，请耐心等待。
[HBuilder] 23:02:51.222 如果是为了三方SDK调试，请使用自定义调试基座（菜单运行-手机或模拟器-制作自定义调试基座），不要反复打包。
[HBuilder] 23:02:51.222 项目 RuleApp-master [__UNI__xxxxxxx]已成功提交到云端，正在获取打包状态...
23:02:52.421 
[HBuilder] 23:02:52.423 项目 RuleApp-master [__UNI__xxxxxxx]的打包状态：
[HBuilder] 23:02:52.423 时间: 2023-11-17 23:02:50    类型: Android云端证书    准备打包    
23:02:52.903 
[HBuilder] 23:02:52.903 项目 RuleApp-master [__UNI__xxxxxxx]的打包状态：
[HBuilder] 23:02:52.903 时间: 2023-11-17 23:02:50    类型: Android云端证书    准备打包    
23:03:49.917 
[HBuilder] 23:03:49.917 项目 RuleApp-master [__UNI__xxxxxxx]的打包状态：
[HBuilder] 23:03:49.917 时间: 2023-11-17 23:02:50    类型: Android云端证书    正在云端打包    
[HBuilder] 23:03:49.917 预计2-5分钟完成打包。如项目使用了App原生插件，打包时间可能会更长，请耐心等待。
23:04:58.053 
[HBuilder] 23:04:58.054 项目 RuleApp-master [__UNI__xxxxxxx]的打包状态：
[HBuilder] 23:04:58.054 时间: 2023-11-17 23:02:50    类型: Android云端证书    正在本地生成安装包(请勿关闭HBuilderX)    
23:05:27.519 
[HBuilder] 23:05:27.519 项目 RuleApp-master [__UNI__xxxxxxx]的打包状态：
[HBuilder] 23:05:27.519 时间: 2023-11-17 23:02:50    类型: Android云端证书    打包成功    安装包位置：xxxxxxxxxxxxxxxxxxxx/apk/__UNI__xxxxx__20231117230250.apk
```
打包好的文件根据提示可以找到。

### 注意事项
#### 阿里云OSS防盗链
需要在防盗链白名单中添加"-"，否则app端资源无法显示。
#### 添加表情包
资源位于`RuleApp-master\unpackage\dist\dev\app-plus\static\app-plus\owo`、`RuleApp-master\static\app-plus\owo`以及`C:\Users\Tamakyi Gekka\Downloads\RuleApp-master\RuleApp-master\static\h5\owo`下。需要修改`OwO.js`并根据路径添加对应资源。


  [1]: https://share.tama.guru/d/App/Android/latest_release.apk
  [2]: https://share.tama.guru/d/App/iOS/%E7%8B%BC%E7%9A%84%E5%85%AC%E5%91%8A%E6%9D%BF.mobileconfig
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65576a92ee3f8.png
  [4]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65576aa82e79e.png
  [5]: http://shell.ruletree.club/ruleapi.sh
  [6]: https://share.tama.guru/%E8%BF%9C%E7%A8%8B%E4%B8%8B%E8%BD%BD%EF%BC%88%E4%BB%93%E5%BA%93%EF%BC%89/ruleproject/java
  [7]: https://share.tama.guru/%E8%BF%9C%E7%A8%8B%E4%B8%8B%E8%BD%BD%EF%BC%88%E4%BB%93%E5%BA%93%EF%BC%89/ruleproject
  [8]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577208c1bb8.png
  [9]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/6557722f7f421.png
  [10]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/6557734790de7.png
  [11]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/655773783661e.png
  [12]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/655773af8f48f.png
  [13]: https://www.ijkxs.com/9955.html
  [14]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577452b2529.png
  [15]: https://account.dcloud.net.cn/
  [16]: https://dcloud.io/hbuilderx.html
  [17]: https://gitee.com/ruletree/RuleApp
  [18]: https://github.com/buxia97/RuleApi
  [19]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/6557762f65a2d.png
  [20]: https://dev.dcloud.net.cn/pages/app/detail
  [21]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/655778cf37399.png
  [22]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/6557798cb22be.png
  [23]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577a0903248.png
  [24]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577a30b67f8.png
  [25]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577acb6c90f.png
  [26]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577b174db42.png
  [27]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/11/17/65577b62916d8.png