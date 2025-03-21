---
title: "在兰空图床配置并使用Cloudflare R2 存储桶"
categories: [ "记录" ]
tags: [ "lskypro","图床","cloudflare","R2" ]
draft: false
slug: "r2_on_lskypro"
date: "2024-01-08 16:00:00"
---

### 什么是R2，为什么用R2

R2是由Cloudflare推出的对象存储，接口兼容AWS S3且有免费套餐，套餐内容如下：

|       类别       |   永远免费   |      按月收费      |
| :--------------: | :----------: | :----------------: |
|       存储       |   10GB/月    | 0.015 美元/GB 存储 |
| A类操作:状态改变 | 100 万次/月  |  4.50 美元/百万次  |
| B类操作:读取状态 | 1000 万次/月 |  0.36 美元/百万次  |

根据文档，各类操作包括：
<center>A类操作</center>

|             操作名称            |                   操作含义                   |
|:-------------------------------:|:--------------------------------------------:|
|           ListBuckets           |            列出的存储桶及其所有者            |
|            PutBucket            |                  创建存储桶                  |
|           ListObjects           |              返回存储桶中的对象              |
|            PutObject            |                   创建对象                   |
|            CopyObject           |                   复制对象                   |
|     CompleteMultipartUpload     |                 完成分片上传                 |
|      CreateMultipartUpload      |               创建分片上传任务               |
|       ListMultipartUploads      |             列出分片上传（进度）             |
|            UploadPart           |                   分片上传                   |
|          UploadPartCopy         | 从一个已存在的Object中拷贝数据来上传一个分片 |
|            ListParts            |                   列出分片                   |
|       PutBucketEncryption       |                  存储桶加密                  |
|          PutBucketCors          |    为指定的存储桶设置跨域资源共享CORS规则    |
| PutBucketLifecycleConfiguration |             设置对象生命周期规则             |
<center>B类操作</center>
|             操作名称            |                   操作含义                   |
|:-------------------------------:|:--------------------------------------------:|
|            HeadBucket           |     确定是否存在存储桶以及是否有访问权限     |
|            HeadObject           |      确定是否存在对象以及是否有访问权限      |
|            GetObject            |               获取（下载）对象               |
|           UsageSummary          |                 使用情况摘要                 |
|       GetBucketEncryption       |            获取（下载）加密存储桶            |
|        GetBucketLocation        |           获取（下载）加存储桶位置           |
|          GetBucketCors          |      获取存储桶设置跨域资源共享CORS规则      |
| GetBucketLifecycleConfiguration |             获取对象生命周期规则             |
<center>免费操作</center>
|             操作名称            |                   操作含义                   |
|:-------------------------------:|:--------------------------------------------:|
|           DeleteObject          |                   删除对象                   |
|           DeleteBucket          |                    删除桶                    |
|      DeleteMultipartUpload      |                 中止分片上传                 |
也就是说R2只对存储和状态修改产生费用，下载不计费，对于小网站来说足够了。
注意:看一下这篇文章[https://machbbs.com/hostloc/640526][1]，避免被坑美刀。


### 有什么不足

Cloudflare祝您新的一年红红火火。

### 如何接入

#### Cloudflare网页端配置

配置之前需要把一个域名接入Cloudflare并设置好解析。比如我的域名`i-tama.website`分配一个二级域名`r2.i-tama.website`用于自定义访问域名，解析内容任意即可，因为后面接入的时候会自动修改成CNAME接入。

首先进入Cloudflare仪表盘的R2，点击创建存储桶：

`存储桶名称`：填写3-63个不含特殊字符，比如我使用的`tamabox`。

位置：选择自动，提供位置提示默认是无的情况下会根据位置自动选择，如果是针对中国的网站那最好是修改为亚太地区(APAC)。（注：记住这个APAC，后面会用到）

然后点击创建，创建完成之后进入设置，设置界面有几个需要注意的地方：

名称和S3 API：这两个后面连接需要用得到，可以保存下来。

自定义域：这里就是配置自定义域名的位置，点击`连接域`，填写需要自定义访问的域名（不加https），例如`r2.i-tama.website`，并继续，然后会提示现有DNS记录将变更成CNAME解析，点击连接域之后等待初始化完成，约1-2分钟即可。成功后该域的状态应为`有效`，且访问存储桶为`已允许`。

R2.dev子域：这里配置允许会生成一个`r2.dev`结尾的二级域名用于公共访问，使用这个方式访问可能会有速度限制，但我没尝试过。配置也很简单，点击允许之后输入`allow`即可。

CORS策略：实际使用时可能会碰到跨域错误在这里配置即可，此处不做修改。

以上配置完成后回退到R2仪表盘，进入账户详细信息下的管理R2 API令牌并选择创建API令牌：

令牌名称：任意填写 记得就行。

权限：因为我们是用来做图床的，涉及到文件读写，建议直接配置成管理员读和写。

指定存储桶：建议一个桶一个API，所以我选择仅应用于特定存储桶，并选择刚才创建的桶。

TTL:永久。

客户端IP地址筛选：留空即可。

最后创建API令牌，此时会得到令牌值、访问秘钥ID和机密访问密钥三个值，需要妥善保存好，因为确认之后就不会再出现这些数据了。

至此，Cloudflare网页端配置完成。

#### 图床端配置

登录管理员账号进入系统设置 > 存储策略，选择新建一个存储策略。

角色组根据自己的选择去配置即可。

名称和简介自行设置好即可。

存储策略选择AWS S3。

访问域名填写在Cloudflare设置的自定义域，比如我的是`https://r2.i-tama.website/tamabox`。

**注意，这里的格式是自定义域名+桶名称，如果只填写域名，后面上传的时候会发现我们上传的图片a.png会被上传到`https://r2.i-tama.website/tamabox/%year%/%month%/%day%/a.png`，但图床中显示的链接是`https://r2.i-tama.website/%year%/%month%/%day%/a.png`，结果就是404了。**

AccessKeyId和SecretAccessKey即前面提到的访问秘钥ID以及机密访问秘钥。

连接地址填写S3 API。

区域就是前面我们配置位置信息的简码，如果我们选择亚太这里就是填apac。

储存桶名称即桶名称。

是否使用路径样式**不要勾选**，因为勾选之后上传到R2会再多一层路径。比如原来上传的位置是`https://r2.i-tama.website/tamabox/%year%/%month%/%day%/a.png`，勾选之后就变成`https://r2.i-tama.website/tamabox/tamabox/%year%/%month%/%day%/a.png`了。

是否在文件上设置公开ACL，这里打开，保存之后随意上传个文件测试是否能上传成功，一般按这个步骤是不会有问题的。

### 体验如何

我没开代理的情况下上传大概是200-300kb/s，下载差不多也是这个速度，只能说基本够用。


  [1]: https://machbbs.com/hostloc/640526