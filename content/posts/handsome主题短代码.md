---
title: "handsome主题短代码"
categories: [ "记录" ]
tags: [  ]
draft: false
slug: "hs-shortcode"
date: "2022-07-12 01:15:00"
---

来源：[短代码 - Handsome 使用文档 (ihewro.com)](https://auth.ihewro.com/user/docs#/preference/shortCode)

以下进行搬运：

 - 时间线
```
[timeline title="2020大事记" type="small" start="梦开始的地方" end="新的一年开始"]
[item date="2020-1-20"] 武汉封城，疫情让一起变得慌乱起来 [/item]
[item date="2020-3-20"] 开始远程办公，慢慢有序起来[/item]
[item]生活变得好起来了[/item]
[item date="2020-10-1"] 国庆节去哈尔滨旅行，和老同学见面[/item]
[/timeline]
```
备注：
timeline 属性列表：

type：可选项，默认为small， 可选值:
small 小巧款式的时间线
large 大款式的时间线
random，可选项，默认为true，可选值为true、false，true表示时间线每一条会使用随机的色彩
对于 large样式的时间线，提供时间线开始和结束的一个文字自定义：

start：一些简单的文字
end:一些简单的文字
item 属性列表：

date ：可选项，默认不显示时间，可选值：
一个日期文字，实际上可以填写任何类型文字（不要太长）
color：可选项，设置当前时间线条的颜色，可选值："light", "info", "dark", "success", "black", "warning", "primary", "danger"


----------

 - 计划表
```
[goal title="2021的小目标"]
[item check="true"] 每天早睡 [/item]
[item check="false"] 每天早起 [/item]
[item progress="50%"] 阅读10本书 [/item]
[item start="2021-01-02" end="2021-10-1"] 每天锻炼30分钟 [/item]
[/goal]
```
备注：
计划表中包含了三种类型的任务：
check类型，该类型任务只有两种状态，完成和未完成
progress，该类型任务可以显示任务执行的进度，需要手动填写进度
start、end，该类型任务设置一个起始时间和终止时间，进度会根据当前时间自动更新进度
时间格式：必须是正确的时间格式，比如2021-01-02 12:00 ，也可以只写日期2021-01-02

----------

 - 音乐
云解析歌曲：支持以下媒体：
```
网易云音乐 http://music.163.com
单曲 http://music.163.com/#/song?id=424474911
专辑 http://music.163.com/#/album?id=34808540
歌手 http://music.163.com/#/artist?id=3681
歌单 http://music.163.com/#/playlist?id=436843836
榜单 http://music.163.com/#/discover/toplist?id=60198

QQ 音乐 https://y.qq.com
单曲 https://y.qq.com/n/yqq/song/000jDQWP4JiB3y.html
专辑 https://y.qq.com/n/yqq/album/003rytri2FHG3V.html
歌手 https://y.qq.com/n/yqq/singer/003Nz2So3XXYek.html
歌单 https://y.qq.com/n/yqq/playlist/1144188779.html

虾米音乐 http://www.xiami.com or http://h.xiami.com
单曲 http://www.xiami.com/song/bf08DNT3035f
专辑 http://www.xiami.com/album/ddOZW6a10eb
歌手 http://www.xiami.com/artist/be6yda0f8
歌单 http://www.xiami.com/collect/254478782

酷狗音乐 http://www.kugou.com
单曲 http://www.kugou.com/song/#hash=09E8DE70A24C97B92A29F6A19F3528A2
专辑 http://www.kugou.com/yy/album/single/1645030.html
歌手 http://www.kugou.com/yy/singer/home/3520.html
歌单 http://www.kugou.com/yy/special/single/119859.html

百度音乐 http://music.baidu.com/
单曲 http://music.baidu.com/song/268275324
专辑 http://music.baidu.com/album/268275533
歌手 http://music.baidu.com/artist/1219
歌单 http://music.baidu.com/songlist/364201689
```

本地mp3地址播放：
```
//单首歌曲
[hplayer]
[Music title="歌曲名" author="歌手" url="http://" pic="图片文件URL" lrc="歌词文件URL" type="mini"/]
[/hplayer]

//多首歌曲
[hplayer]
[Music title="歌曲名" author="歌手" url="http://" pic="图片文件URL" lrc="歌词文件URL" type="mini"/]
[Music title="歌曲名" author="歌手" url="http://" pic="图片文件URL" lrc="歌词文件URL" type="mini"/]
[/hplayer]
```
其中lrc 字段可以不填，表示没有歌词,type 可选择为 normal、mini 两种



----------

 - 视频
点击编辑器的插入视频图标可以插入视频，支持本地的视频地址的播放和解析第三方平台的视频
时光机的视频插入按钮也是同样的操作
```
[vplayer url="http://xxx.com/xxx.mp4" pic="http://xxx.com/xxx.png"]
```
url：视频地址
pic：视频背景图片


----------

 - 按钮

```

[button color="danger" icon="fontello fontello-gratipay" url="http:\/\/wpa.qq.com\/msgrd?v=3&uin=535425690&site=qq&menu=yes"]点击QQ咨询购买（√推荐）[/button]

[button color="success" icon="glyphicon glyphicon-send" url="mailto:ihewro@163.com"]ihewro#163.com[/button]

[button color="success" icon="music" url="mailto:ihewro@163.com"]音乐歌单[/button]

[button color="success" icon="🍜" url="mailto:ihewro@163.com"]菜谱[/button]
```
备注：

内容可替换如下：
矩形文字按钮
椭圆形文字按钮
矩形图标文字按钮
例如：
```
[button]矩形按钮文字[/button]

[button color="succees"]矩形按钮文字[/button]

[button type="round" color="success"]椭圆形按钮文字[/button]

[button color="success" icon="glyphicon glyphicon-eur"]图标按钮文字[/button]
```
type：选填，不填默认为矩形按钮。可选值：
round：椭圆形按钮
color： 选填，不填默认为success（绿色），可选值：
light：白色
info：蓝色
dark：深色
success：绿色
black：黑色
warning：黄色
primary：紫色
danger：红色
icon：选填，不填默认为文字按钮
url:选填，填写点击的链接。举例：如果你的链接是http://www.baidu.com，请手动将/前面加一个反斜杆\，即http:\/\/www.baidu.com


----------

 - 链接卡片

```
[link]
[item name="百度一些" link="http://baidu.com" desc="百度一下你就知道" /]
[item name="网站名称" link="http://example.com" desc="一些链接描述" /]
[item name="网站名称" link="http://example.com" desc="一些链接描述" pic="图片地址" /]
[/link]
```

----------

 - 文章卡片
点击编辑器的调用文章的按钮可以调用其他文章并显示。
```
[post cid="XXX" /]
[post cid="XXX" cover="http:\/\/localhost\/build\/usr\/themes\/handsome\/usr\/img\/sj\/6.jpg"/]
```
cid：必填，是文章的唯一编号，在后台的编辑文章的地址可以看到，比如http://localhost/build/admin/write-post.php?cid=23,值为23
cover：可选项，调用文章显示的封面，如果不填则默认显示随机缩略图。
size：可选项，可填写值large 、small，默认为large。small表示只显示文章标题，不显示其他额外信息。


----------

 - 外链卡片
```
[post url="XXX" title="XXX" intro="XXX" cover="XXXX" /]
```
title：必填，外链的名称/标题
url：必填，外链的地址（请填写绝对地址，就是浏览器地址栏什么样的就填什么样的）
intro：（可选）外链网站的简介/介绍
cover：（选填）外链网站的介绍图片地址

----------

 - 相册与图集
相册与图集的区别是：

相册样式是固定一行N个，N会根据屏幕尺寸调整
图集会自动将图集内部的所有图片显示在一行中（如果图片太多，需要分成多个图集），并且大小会自动调整
相册
```
[album]
[普通的图片插入，支持markdown语法和html语法，混合也可以]
[/album]
```
图集：增加type="photos" 则album下的图片排成一行，并根据图片的长高比自动排列
```
[album type="photos"]
[普通的图片插入，支持markdown语法和html语法，混合也可以]
[/album]
```

----------

 - 回复可见
```
[hide]需要隐藏的内容[/hide]
```

----------

 - 登陆可见
```
[login]需要加密的内容[/login]
```

----------

 - 高亮提示文本
```
[scode type="share"]这是灰色的短代码框，常用来引用资料什么的[/scode]
[scode type="yellow"]这是黄色的短代码框，常用来做提示，引起读者注意。[/scode]
[scode type="red"]这是红色的短代码框，用于严重警告什么的。[/scode]
[scode type="lblue"]这是浅蓝色的短代码框，用于显示一些信息。[/scode]
[scode type="green"]这是绿色的短代码框，显示一些推荐信息。[/scode]
```
高亮提示文本有三种样式，默认的样式如上，可以通过"size" 属性来修改:
```
[scode type="red" size="simple"]该样式不包含左侧的图标，为简单样式[/scode]
[scode type="red" size="small"]该样式为文字背景，不包含图标和过多的边距[/scode]
```

----------

 - 收缩框
示例代码：
```
[collapse title="标题" status="false"]一些文字内容[/collapse]
[collapse title="标题"]一些文字内容[/collapse]
```
其中status为false 表示默认不显示文字内容，为true表示默认显示内容。
不写status 属性，默认是展开内容的。

----------

 - 标签卡
示例代码：
```
[tabs]
[tab name="标签页 1" active="true"]内容 1[/tab]
[tab name="标签页 2"]内容 2[/tab]
[/tabs]
```
每个tab 都必须包括name属性，只能有一个tab 添加active属性，表示默认显示该tab的内容。

此外tab支持更多css的属性，比如颜色，字体粗细等：
[tab name="标签页 2" color:"#000" font-weight="bold"]高级内容文本示例[/tab]

----------

 - 标签tag
示例代码：
```
[tag]默认白色文字[/tag]
[tag type="primary"]紫色文字[/tag]
[tag type="info"]蓝色文字[/tag]
[tag type="warning"]黄色文字[/tag]
[tag type="danger"]红色文字[/tag]
[tag type="success"]绿色文字[/tag]
[tag type="dark"]黑色文字[/tag]
```
----------

 - 文章中插入多栏

size属性可以填写百分比或者固定的宽度值（单位是px）
```
//一个简单的例子
[colum]
[block size="20%"]百分比宽度[/block]
[block size="200px"]固定宽度[/block]
[block]标题3[/block]
[/clumn]
```
```
// 一个复杂一点的例子，block内部可以使用其他的短代码带来更丰富的排版
[column]
[block size="20%"]
[scode type="red" size="small"]标题1[/scode]
[scode type="share" size="small"]标题1
标题1的内容
[/scode]
[/block]

[block size="200px"]
[scode type="red" size="small"]标题2[/scode]
[/block]

[block]
[scode type="red" size="small"]标题3[/scode]
[/block]
[/column]
```