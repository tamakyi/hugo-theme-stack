---
title: "使用acme.sh申请通配ssl证书"
categories: [ "记录" ]
tags: [ "阿里云","acme.sh","zerossl","ssl","腾讯云" ]
draft: false
slug: "acme-sh_zerossl"
date: "2024-05-20 21:04:00"
---

从今年3-4月起，国内主流的域名平台都开始把原来一年期的免费证书调整成三个月（参见：[免费版SSL证书升级指南][1]），但是阿里另外给了个解决方案，单域名一年缴68元可以获得原来一样的一年证书。

> 尊敬的阿里云用户：
> 接收到厂商关于免费证书服务收费策略调整的通知，我们将在2023年11月14号开始生效厂商策略。在策略生效后，免费证书规则如下：
> 对于免费领取的SSL证书，签发后的证书有效期统一变更为3个月。如果您希望继续使用12个月有效期的免费证书，则需收取相关服务费，费用为68元/张/年。

对于我们这些白嫖用户来说这就非常难搞了，证书时间大幅缩短意味着一年要申请四次，而且如果域名很多的话每个还需要单独申请，想想都很酸爽...但是花钱是不可能的，单个证书68对我们来说也是一笔不小的开销，对我们这种小站站长确实不值得。


<!--more-->


那么现在摆在我们面前的有两条路：
~~1.关站跑路~~
2.想办法做自动化申请替换。

比较常见的方法就是[acme.sh][2]，这是个可以申请签发、部署管理、ssl证书的脚本，操作起来也比较方便，甚至可以通过corntab之类的工具实现自动续期，算是当前状况下一个解决方案了。

而证书提供商我推荐使用[zerossl][3]，最主要的是它能提供免费的范域名甚至是IP证书，虽然只有三个月，但是结合acme.sh就能实现自动续签，相当于无限续期。
### 正常操作流程
首先肯定是先去zerossl申请一个账号，这个不多说了。
接下来安装acme.sh
如果服务器在国外的，直接使用官方的
    curl https://get.acme.sh | sh
即可，如果服务器在国内的，考虑到直连github可能会抽风，建议使用
    curl https://gitee.com/acmesh-offical/acme.sh/raw/master/acme.sh | sh -s email=your@zerosslaccount.com
其中`your@zerosslaccount.com`换成自己申请zerossl账号用的邮箱。
然后使环境生效：
   source ~/.bashrc
使用第一种方法会安装到`~/.acme.sh/`下，不执行环境变量生效的可以手动在文件夹内执行脚本。
然后把zerossl配置为默认的ca服务：
    acme.sh --set-default-ca --server zerossl
因为当前我使用的是DNSPOD的解析，需要先去[DNSPOD Token][4]页面生成一组密钥，生成之后页面会生成一个ID和TOKEN，假设：
    ID=XXXXXX
    TOKEN=QWERTYUIOPASADFGHJKL
那么需要配置以下内容到环境变量：
    export DP_Id="XXXXXX"
    export DP_Key="QWERTYUIOPASADFGHJKL"
这里的DP指的是DNSPOD，如果使用的是其他服务商可以参考[dnsapi文档][5]和[acme.sh wiki][6]。
然后配置zerossl的账号信息到acme.sh:
    acme.sh --register-account -m your@zerosslaccount.com --server zerossl
这里会生成一个`ACCOUNT_THUMBPRINT`，一般用不到，可以忽略。
到此就可以进行证书申请操作了：
比如我要申请的证书域名是`*.tama.guru`，那么需要执行：
    acme.sh --dns dns_dp --issue -d *.tama.guru -d tama.guru
如果执行过程中报错可以执行：
    acme.sh --dns dns_dp --issue -d *.tama.guru -d tama.guru --debug
来检查是什么步骤报错。
如果没有任何错误，目录结构应该如下：

    -********-  8 root root    187 May 20 17:17  .
    -********- 24 root root   4096 May 20 20:28  ..
    -********-  1 root root    433 May 20 16:56  account.conf
    -********-  1 root root 223427 May 20 16:29  acme.sh
    -********-  1 root root     78 May 20 16:29  acme.sh.env
    -********-  3 root root     30 May 20 16:48  ca
    -********-  2 root root   4096 May 20 16:29  deploy
    -********-  2 root root   8192 May 20 16:29  dnsapi
    -********-  1 root root    221 May 20 17:17  fetch.sh
    -********-  1 root root    393 May 20 16:56  http.header
    -********-  2 root root   4096 May 20 16:29  notify
    -********-  2 root root    162 May 20 16:56 '*.tama.guru_ecc'
我们的证书就保存在`'*.tama.guru_ecc'`内，配置`*.tama.guru.key`和`fullchain.cer`到nginx/apache上即可。
### 自动化
根据上面的步骤基本上就知道自动化脚本怎么写了:
    # nano /root/.acme.sh/autocert.sh    
                                                                                                          
    #!/bin/bash
    cd /root/.acme.sh #根据acme安装路径选择
    export DP_Id="XXXXXX"
    export DP_Key="QWERTYUIOPASADFGHJKL"
    #./acme.sh --register-account -m your@zerosslaccount.com --server zerossl
    ./acme.sh --dns dns_dp --issue -d *.tama.guru -d tama.guru

然后添加到crontab:
    0 23 1 * * sh nano /root/.acme.sh/autocert.sh #每月1号的23点执行以上脚本
如果acme.sh所在机器就是网站服务器，可以参照[Using acme.sh with nginx][7]把应用证书的工作也交给服务器自动完成，更简单的方法就是先指定输出文件位置然后直接在nginx的配置文件里面修改:
    #导出文件
    acme.sh --install-cert -d tama.guru --key-file       /path/to/keyfile/tama.guru.pem  --fullchain-file /path/to/fullchain/tama.guru.cer --reloadcmd     "service nginx force-reload"
    #修改nginx配置文件
    ssl_certificate /path/to/fullchain/tama.guru.cer;
    ssl_certificate_key /path/to/keyfile/tama.guru.pem;
### 其他
Q：我不想敲一堆命令，有更直观的方法吗？
A：可以使用开源工具[certd][8]，该工具提供了一个网页前端供用户配置自动化流水线申请证书。
Q：那你为什么不用？
A：它只支持Let's encrypt证书。


  [1]: https://help.aliyun.com/zh/ssl-certificate/user-guide/upgrade-a-free-certificate-upgrade-a-free-certificate
  [2]: https://github.com/acmesh-official/acme.sh
  [3]: https://zerossl.com
  [4]: https://console.dnspod.cn/account/token/token
  [5]: https://github.com/acmesh-official/acme.sh/tree/master/dnsapi
  [6]: https://github.com/acmesh-official/acme.sh/wiki
  [7]: https://www.rmedgar.com/blog/using-acme-sh-with-nginx/
  [8]: https://github.com/certd/certd