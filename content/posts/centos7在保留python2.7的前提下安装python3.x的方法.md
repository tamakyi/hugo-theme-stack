---
title: "centos7在保留python2.7的前提下安装python3.x的方法"
categories: [ "记录" ]
tags: [ "centos7","python" ]
draft: false
slug: "centos7_python3-x"
date: "2022-09-17 01:46:10"
---

  不会有人2022年了还在用centos7吧。。。
  由于centos7默认安装的是python2.7且系统内组件高度依赖python2.7，如果直接卸载换成3.X会导致许多系统软件无法运行(yum就是一个很好的例子），因此想法如下：
·编译安装3.X版本
·把原来的python软链接指向3.x，并创建pip3的软链接
·把相关软件的头手动进行修改
  例如`/usr/libexec/urlgrabber-ext-down`中原来的`#!/usr/bin/python`应手动指定为`#!/usr/bin/python2.7`，`/usr/bin/yum`同理。
  参考：[https://www.cnblogs.com/jizhong/p/15157828.html][1]


  [1]: https://www.cnblogs.com/jizhong/p/15157828.html