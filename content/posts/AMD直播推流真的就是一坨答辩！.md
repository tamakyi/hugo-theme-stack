---
title: "AMD直播推流真的就是一坨答辩！"
categories: [ "默认分类" ]
tags: [  ]
draft: false
slug: "adm_stream"
date: "2023-05-07 16:57:09"
---

AMD在编解码这一块就是一坨大便！
最近把平台从Intel转到AMD，现在家里的两台机器已经是清一色的红厂CPU了。
<table>
<thead>
  <tr>
    <th colspan="2">主电脑</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>CPU:</td>
    <td>5700X</td>
  </tr>
  <tr>
    <td>显卡：</td>
    <td>3060ti</td>
  </tr>
  <tr>
    <td colspan="2">备胎机</td>
  </tr>
  <tr>
    <td>CPU:</td>
    <td>2200G</td>
  </tr>
  <tr>
    <td>显卡：</td>
    <td>核显Vega8</td>
  </tr>
</tbody>
</table>
自己打打游戏都还好，但是在换上新CPU的第一次直播我就发现，我这端开始提示`编码过载`，然后别人看直播都是一卡一卡的。这就很奇怪，明明配置比之前高了不少，而且设置也没改过，怎么会这样呢？水土不服？无论是奥德赛、欧卡，甚至是Muse Dash这种不是很依赖3D性能的游戏都很卡。
![这是当前的OBS配置，没有改动过][1]
我自己玩起来是一点卡顿都没有的，那么有问题的很可能就是CPU。10400换了5700X之后，最重要的一个地方是核显没了，是不是这个原因呢？经过一番搜索，比如这里：[OBS编码详解][2]
![下面的评论][3]
[一个带核显的CPU好 还是配了同样性能的独显好？][4]
![下面的评论][5]
AMD有这么拉吗？我平台都换了，总不能再换回去吧...
我试想了几个解决方案：

 1. 通过内网用二胎机用VEGA8做推流编码工作。
主要是看到这篇文章：[https://zhuanlan.zhihu.com/p/49187146][6]

> 游戏机只负责玩游戏，由于游戏机通过显卡连了根线到采集卡，所以他把采集卡当做一块显示器，采集卡便采集这块“显示器”的视频数据，并因为装在直播主机上，使得该数据可以被直播机（OBS）捕获（直播主机把采集卡当做一个视频输入源，就像直播机外接了一个摄像头一样）。这样一来，游戏机借由另一台电脑来直播，自身无需承担任何直播相关的性能压力。

这个情况和我现在的需求倒是挺像的，也确实有一台可以用作直播主机的机器，问题就是采集卡，好像也有办法直接通过软件推流到另一台电脑上而不需要通过采集卡，还有就是VEGA8能不能应付这个情况？稍微搜索了一下，发现如果要用采集卡的话对卡的质量要求挺高的（也挺贵的），而且我的备胎机是在另一个房间里，那么长的距离传输对延迟和各方面是不是都有一些影响？
由于备胎平时还要用作NAS，本身机箱空间就小，平时温度挺高的，说实话我不是很想用这个方式，怕的是再加上编码这个活儿稳定性会大幅降低。

 2. 再加个核显
也是偶然看到有[https://zhuanlan.zhihu.com/p/617531197][7]这个文章，第一次知道有外置核显这种玩意 ::acfun:02:: 
![DG1外观][8]
参数（来源[Intel Ark][9])：
<table>
<thead>
  <tr>
    <th>ARK | 比較 Intel® 產品</th>
    <th></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>2023/05/07 16:53:56</td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td>Intel® Iris® Xe 獨立顯示卡 (80 EU)</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>關鍵元件</td>
    <td></td>
  </tr>
  <tr>
    <td>產品系列</td>
    <td>Intel® Iris® Xe 顯示晶片獨立 GPU</td>
  </tr>
  <tr>
    <td>代號</td>
    <td>產品原名&nbsp;&nbsp;DG1</td>
  </tr>
  <tr>
    <td>狀態</td>
    <td>Launched</td>
  </tr>
  <tr>
    <td>推出日期</td>
    <td>Q1'21</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>GPU 規格</td>
    <td></td>
  </tr>
  <tr>
    <td>繪圖最大動態頻率</td>
    <td>1500 MHz</td>
  </tr>
  <tr>
    <td>TDP</td>
    <td>28 W</td>
  </tr>
  <tr>
    <td>PCI Express 設定 ‡</td>
    <td>Up to x4 Gen4</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>記憶體規格</td>
    <td></td>
  </tr>
  <tr>
    <td>繪圖記憶體介面</td>
    <td>128 bit</td>
  </tr>
  <tr>
    <td>繪圖記憶體頻寬</td>
    <td>68 GB/s</td>
  </tr>
  <tr>
    <td>繪圖記憶體速度</td>
    <td>4267 Gbps</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>支援的技術</td>
    <td></td>
  </tr>
  <tr>
    <td>DirectX* 支援</td>
    <td>12.1</td>
  </tr>
  <tr>
    <td>Vulkan* 支援</td>
    <td>1</td>
  </tr>
  <tr>
    <td>OpenGL* 支援</td>
    <td>4.6</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>I/O 規格</td>
    <td></td>
  </tr>
  <tr>
    <td>支援的顯示器數量 ‡</td>
    <td>3</td>
  </tr>
  <tr>
    <td>圖形輸出‡</td>
    <td>DP 1.4 w/ HDR |&nbsp;&nbsp;HDMI 2.0b</td>
  </tr>
  <tr>
    <td>最大解析度 (HDMI)‡</td>
    <td>4096 x 2160@60Hz</td>
  </tr>
  <tr>
    <td>最大解析度 (DP)‡</td>
    <td>7680 x 4320@60Hz</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>功能</td>
    <td></td>
  </tr>
  <tr>
    <td>H.264 硬體編碼/解碼</td>
    <td>是</td>
  </tr>
  <tr>
    <td>H.265 (HEVC) 硬體編碼/解碼</td>
    <td>Yes |&nbsp;&nbsp;12-bit</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>進階技術</td>
    <td></td>
  </tr>
  <tr>
    <td>Intel® Adaptix™ 技術</td>
    <td>否</td>
  </tr>
  <tr>
    <td>Intel® Deep Learning Boost</td>
    <td>是</td>
  </tr>
</tbody>
</table>
看起来好像还真有点那回事。今天下单了一张，过段时间到货了测试看看。


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/64575e21706ce.png
  [2]: https://zhuanlan.zhihu.com/p/448543411
  [3]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/6457602a1607b.png
  [4]: https://www.zhihu.com/question/578864076/answer/2943667852?utm_id=0
  [5]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/645763c0044d8.png
  [6]: https://zhuanlan.zhihu.com/p/49187146
  [7]: https://zhuanlan.zhihu.com/p/617531197
  [8]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2023/05/07/645766a9cb0b8.png
  [9]: https://ark.intel.com/content/www/tw/zh/ark/products/211014/intel-iris-xe-dedicated-graphics-card-80-eu.html