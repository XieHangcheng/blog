---
layout: post
title:  "UCloud上采用对象存储存储数据"
subtitle: '针对UCloud的负载均衡配置'
date:   2020-07-21 10:00:21 +0800
tags: ucloud
color: rgb(255,90,90)
cover: '../assets/20200721/cover.jpg'
---

继续暑期校内实习的内容，通过UCloud控制台及filemgr管理对象文件
狭义的对象存储是提供海量简单KV存储模型的分布式存储系统
* 对象（Object）
* 对象存储设备（OSD）
* 元数据服务器（MDS）
* 对象存储客户端

对象存储的优势：
* 高可扩展性
* 高可用性
* 自动容灾备份
* 降低TCO（成本）
* 高性能


## UCloud创建对象存储空间UFile
1. 在UCloud控制台中选择**全部产品**，**对象存储UFile**

   ![ufile.jpg](/assets/20200721/ufile.jpg)
   
2. 创建存储空间Bucket

   点击**创建存储空间**
   ![create_bucket.jpg](/assets/20200721/create_bucket.jpg)
   填写Bucket相关信息，点击**确定**
   ![bucket_info.jpg](/assets/20200721/bucket_info.jpg)
   此时Bucket已创建，接下来测试该存储空间，点击**文件管理**进入管理页面
   ![bucket_list.jpg](/assets/20200721/bucket_list.jpg)
   点击**上传文件**
   ![upload.jpg](/assets/20200721/upload.jpg)
   弹出选项卡，填入相关信息，**存储类型**选择**标准存储**，之后点击**添加文件**即可完成上传
   ![upload_info.jpg](/assets/20200721/upload_info.jpg)
   回到文件管理页面，找到刚刚上传的文件，在右边点击**获取地址**即可在弹出的对话框中得到文件的URL链接
   ![get_addr.jpg](/assets/20200721/get_addr.jpg)
   ![addr.jpg](/assets/20200721/addr.jpg)
   在浏览器中输入得到的URL链接，即可访问我们刚刚上传的文件

   文件若是图片、视频或者PDF等，会通过浏览器直接打开，而其他文件则会转为下载
   ![test_url.jpg](/assets/20200721/test_url.jpg)
   接下来测试归档存储，如图在文件管理页点击**修改存储类型**
   ![chtype.jpg](/assets/20200721/chtype.jpg)
   将存储类型改为**归档存储**，点击**确定**
   ![chtype2.jpg](/assets/20200721/chtype2.jpg)
   此时通过URL访问该资源会发现无法访问，因为该资源已被归档冻结
   ![test_lock.jpg](/assets/20200721/test_lock.jpg)
   回到文件管理页，如图点击**解冻**
   ![unlock.jpg](/assets/20200721/unlock.jpg)
   解冻完成，再通过URL便可以重新访问该资源了
   ![test_unlock.jpg](/assets/20200721/test_unlock.jpg)
   

## 使用filemgr管理对象文件
1. 首先为存储空间创建一个令牌token，在存储空间管理页中点击**令牌管理**，**创建令牌**
   ![create_token.jpg](/assets/20200721/create_token.jpg)
   在弹出的选项卡中填写token信息，此时权限仅勾选**上传**和**下载**
   ![token_info.jpg](/assets/20200721/token_info.jpg)
   然后即可看到创建好的令牌，以及其**公钥**和**私钥**
   ![token_list.jpg](/assets/20200721/token_list.jpg)
2. 到UCloud控制台页面下载filemgr程序，如图
   ![tools.jpg](/assets/20200721/tools.jpg)
   下载完成后解压缩至指定目录，打开config.cfg文件，将刚刚创建的令牌的公钥和私钥填入指定位置，保存
   ![cfg.jpg](/assets/20200721/cfg.jpg)
   filemgr程序需要通过cmd运行，因此打开cmd，切换路径至filemgr程序根目录，输入以下命令可进行文件上传操作

   bucketname改为自己待传入的bucket的名称，filename是存入后的文件名称，route为待上传文件本地的路径
   ```
   filemgr-win64.exe --action mput --bucket bucketname --key filename --file route
   ```
   ![run.jpg](/assets/20200721/run.jpg)
   测试删除文件，使用以下命令：
   ```
   filemgr-win64.exe --action delete --bucket bucketname --key filename
   ```
   ![delete.jpg](/assets/20200721/delete.jpg)
   发现删除失败，这是因为我们之前创建令牌时未赋予删除权限，现在我们为令牌重新添加删除权限，再次尝试删除
   ![delete_suc.jpg](/assets/20200721/delete_suc.jpg)
   删除成功
   