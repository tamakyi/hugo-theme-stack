---
title: "在debian11安装并使用wifite"
categories: [ "默认分类" ]
tags: [  ]
draft: false
slug: "wifite_in_debian11"
date: "2022-07-02 20:24:31"
---

在debian11直接apt install -y wifite 并运行会提示：
 [!] Warning: Recommended app bully was not found. install @ https://github.com/aanarchyy/bully
 [!] Warning: Recommended app pyrit was not found. install @ https://github.com/JPaulMora/Pyrit/wiki
 [!] Warning: Recommended app hashcat was not found. install @ https://hashcat.net/hashcat/
 [!] Warning: Recommended app hcxdumptool was not found. install @ apt install hcxdumptool
 [!] Warning: Recommended app hcxpcapngtool was not found. install @ apt install hcxtools
 [!] Warning: Recommended app macchanger was not found. install @ apt install macchanger
接下来把他需求的所有组件都安装一遍：
①bully:这个安装很简单
git clone https://github.com/aanarchyy/bully
cd bully/src/
make && make install
②pyrit：注意的是pyrit需要python2.7环境，需要先手动安装python2.7:
 apt install -y python2.7 python2.7-dev
 git clone https://github.com/JPaulMora/Pyrit.git
 pip install psycopg2   
 pip install scapy
 cd pyrit && python2 setup.py clean && python2 setup.py build
 python2 setup.py install
③hashcat：hashcat依赖libconv组件，有可能安装了它找不到，因此可按以下步骤：
 wget https://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.17.tar.gz
 tar zxvf libiconv-1.17.tar.gz && cd libiconv-1.17
 ./configure --prefix=/usr/local
 make && make install
有可能还是会出错，搜索一下只不是有多个iconv.h
 find / -name iconv.h
果然在/usr/local/include下有一个，这个需要改名
 mv /usr/local/include/iconv.h  /usr/local/include/iconv.h.bak
之后安装hashcat:
 wget https://hashcat.net/files/hashcat-6.2.5.7z
 tar zxvf hashcat-6.2.5.7z
 cd hashcat-6.2.5
 make && make install