---
title: 给学生搭个网盘
categories: 网盘
tags: [网盘]
date: 2021-08-28  
updated: 2021-08-28

---

## 什么是DzzOffice

DzzOffice是一套开源办公套件，适用于企业、团队搭建自己的类似“Google企业应用套件”、“微软Office365”的企业协同办公平台。

官网地址： http://dzzoffice.com/

Github：https://github.com/zyx0814/dzzoffice/releases/

目前最新版：2.02

官网上没有什么很详细明确的安装教程（官网付费服务，可以理解），网上查了一下，也没有，这里就详细的记录一下 DzzOffice 的开源版的实际部署过程，有任何问题或不明白的欢迎留言。

## 为什么这么做？

首先肯定是自己拥有闲置一台服务器，一直利用不起来，后来想了想，不妨来做一个协同的网盘服务。百度网盘的收发作业实在是不行，有点坑，搞起来很麻烦，大三搞作业给我整崩溃了，实在不是，不好用。
 
## 步骤

1. 准备环境
    
DzzOffice 是 php 的 web 服务，需要 php 的环境和 php 的 mysql 连接模块，还有一个 web 服务器如 apache 或 nginx ，这里我使用 apache 。

```bash

yum install -y httpd php php-mysql

```


这里都是使用的 centos7 默认库的默认 php 版本：5.4.16，也可以用，虽然DzzOffice 推荐是使用 php7

如果要安装php7，则需要先将php的老版本删除，然后执行如下命令进行安装：


```bash
# 查看原本是否有安装php
rpm -qa|grep php
# 如果有输出，将上面的输出，依次用下面命令进行删除，如下：
rpm -e --nodeps php-5.4.16-46.el7.x86_64
rpm -e --nodeps php-cli-5.4.16-46.el7.x86_64
...
 
###  安装 php7
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
yum install -y php72w php72w-cli php72w-common php72w-mysql php72w-pdo
``` 

2. 下载源码启动服务

下载 DzzOffice 开源代码，直接到官方 Github 上下载：

```bash
# 下载最新稳定版本，我现在是2.02为最新版
wget https://github.com/zyx0814/dzzoffice/archive/2.02.tar.gz
 
# 解压文件
tar -zxvf 2.02.tar.gz
 
# 将解压后的文件移动到apache的目录下，并改名为dzzoffice
mv dzzoffice-2.02 /var/www/html/dzzoffice
 
# 然后将目录权限授权给apache启动用户，默认为apache用户，如果自己修改了，则以你修改的为准
cd /var/www/html/
chown -R apache. dzzoffice
 
# 启动apache
systemctl start httpd
systemctl enable httpd    # 设置开机启动apache
 
# 防火墙永久开放80端口，如果防火墙关了就不用执行了
firewall-cmd --add-port=80/tcp --permanent
firewall-cmd --reload
``` 

3. 访问页面进行安装

上一步已启动 apache，现在可以直接访问你服务器的 ip 或域名，后跟 DzzOffice 的路径来来访问 DzzOffice，访问如：http://ip/dzzoffice 会自动跳转到安装界面：

![1](https://moodle-img-video.oss-cn-zhangjiakou.aliyuncs.com/sakaay/dzzoffice/1.png?x-oss-process=style/suolue)

![2](https://moodle-img-video.oss-cn-zhangjiakou.aliyuncs.com/sakaay/dzzoffice/2.png?x-oss-process=style/suolue)

前面对目录文件对授权，这里会检查

![3](https://moodle-img-video.oss-cn-zhangjiakou.aliyuncs.com/sakaay/dzzoffice/3.png?x-oss-process=style/suolue)

 

根据实际情况，填写数据库信息，这里需要自己事先创建数据库，一般公司都会有数据库的，直接拿来用就好，这里就不细说数据库的安装了，有需要的可以参考百度。

![4](https://moodle-img-video.oss-cn-zhangjiakou.aliyuncs.com/sakaay/dzzoffice/4.png?x-oss-process=style/suolue)

4. 安装完成后，手动删除安装文件

```bash
rm -rf /var/www/html/dzzoffice/install/index.php
``` 

5. 安装完成，登录

安装完成，登录后。根据指引进行操作即可，这里就不详细说明了。


6. 安装文档在线浏览编辑工具

DzzOffice 本身不支持 excel 或者文档的在线浏览和编辑，需要额外的第三方工具进行支持，在官方文档中也有说明：http://dzzoffice.com/corpus/list?cid=3#

这里我现在安装onlyoffice作为在线文档服务器，部署方式，由于直接在服务器上部署比较繁琐，这里我直接使用 docker 部署 docker 版本。首先安装 docker，然后用 docker 启动 onlyoffice

```bash
# 安装docker
# 1. 协作旧版本，确保机器没有docker
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2.安装依赖
yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
# 3.安装yum仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 4.安装
yum install docker-ce docker-ce-cli containerd.io
# 启动docker
systemctl start docker
systemctl enable docker
# 启动onlyoffice，使用本地的8000端口
docker run -i -t -d -p8000:80 --restart=always onlyoffice/documentserver

```
   

启动onlyoffice服务后，在浏览器中访问 http://ip:8000 查看是否可以正常使用。


然后在dzzoffice中进行配置：

管理 -》 应用市场 -》 在应用市场内找到 “onlyoffice” 应用 点击 一键安装

管理 -》 应用市场 -》 已安装 中 点击设置按钮进入设置页面


这里填写您的文档服务器的地址：如文档服务器地址为

 http://onlyoffice.dzzoffice.com

文档服务器端口为：90

那么 这里的地址应该是：

http://onlyoffice.dzzoffice.com:90/OfficeWeb/apps/api/documents/api.js

点击保存，然后启动应用

然后在文档，excel应用中，就可以直接点击在线浏览和编辑啦。

OK，到此整个部署过程就完成了，愉快的使用吧。部署过程中有任何疑问或者问题，欢迎留言交流。
