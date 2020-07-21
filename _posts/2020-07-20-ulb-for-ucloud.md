---
layout: post
title:  "通过负载均衡实现UCloud网站的高可用"
subtitle: '针对UCloud的负载均衡配置'
date:   2020-07-20 18:00:21 +0800
tags: ucloud
color: rgb(255,90,90)
cover: '../assets/20200720/cover.jpg'
---

继续暑期校内实习的内容，通过负载平衡实现UCloud网站的高可用

## 从镜像创建新主机
1. 首先通过之前创建的主机镜像创建一台新主机

   进入**镜像管理**选项卡，点击**从镜像创建主机**
   ![iso_m.jpg](/assets/20200720/iso_m.jpg)
   之后的创建主机步骤与一般情况大致一样
2. 主机创建成功后，可以在主机管理页面看到
   ![hosts.jpg](/assets/20200720/hosts.jpg)
   开启新主机，并开启httpd和mysqld服务


## 配置负载均衡ULB
1. UCloud控制台页面左上角选择**全部产品**，在**网络**分类中找到**负载均衡 ULB**，点击进入
   ![ulb.jpg](/assets/20200720/ulb.jpg)
   点击**创建负载均衡**
   ![create_ulb.jpg](/assets/20200720/create_ulb.jpg)
2. 接下来就是选择负载均衡服务的相关配置
   
   首先**地域**选择同主机一致，**负载均衡类型**选择**请求代理型**
   ![ulb_1.jpg](/assets/20200720/ulb_1.jpg)
   **网络模式**选择**外网**，**所属VPC**默认，**弹性IP**选择**新购**，
   **计费方式**选择**带宽**，**实例名称**自定
   ![ulb_2.jpg](/assets/20200720/ulb_2.jpg)
3. 创建成功的负载均衡实例可以在**负载均衡管理**页中看到,并可看到新获得的外网IP
   ![ulb_eip.jpg](/assets/20200720/ulp_eip.jpg)
4. 接下来为负载均衡实例添加VServer，点击实例名称进入管理里界面，点击**添加VServer**
   ![add_server.jpg](/assets/20200720/add_server.jpg)
5. 在弹出的选项卡中填写VServer相关信息，如下图所示，之后点击**确定**
   ![vserver.erjpg](/assets/20200720/vserver.jpg)
6. 添加后进入**VServer管理**选项卡，选择**服务节点**，点击**添加节点**
   ![add_node.jpg](/assets/20200720/add_node.jpg)
7. 在弹出的选项卡中填写节点信息，**监听端口**填写80后可看到可选资源，选中存在的两台主机，点击右移的按钮，点击**确定**即完成节点添加
   ![node_info.jpg](/assets/20200720/node_info.jpg)
   添加的节点可以在**服务节点**选项卡中看到
   ![node_list.jpg](/assets/20200720/node_list.jpg)
   至此负载均衡服务已配置完成

## 负载均衡测试
1. 为体现负载均衡的作用，我们对两台主机稍作改动

   两台主机分别设为主机B、C，主机C为B的镜像，为了区分两台主机上部署的WordPress内容，修改B中的内容如下：
   ![host_B.jpg](/assets/20200720/host_B.jpg)
   C中内容保持不变，如下：
   ![host_C.jpg](/assets/20200720/host_C.jpg)
2. 通过负载均衡服务的外网IP访问部署的WordPress网站，地址为：“ULB_EIP/WordPress/”，访问页面如下，发现显示的是主机B上部署的页面
   ![test_b.jpg](/assets/20200720/test_b.jpg)
   按F5重新加载页面若干次，发现显示的页面会变为主机C上部署的页面，如下：
   ![test_c.jpg](/assets/20200720/test_c.jpg)
   至此证明负载均衡服务生效
   