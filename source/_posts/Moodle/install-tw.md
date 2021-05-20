---
title: 安装Moodle（图文版）
categories: Moodle
tags: Moodle
date: 2021-05-18
updated: 2021-05-18
---

在云服务器的购买（图文版）中，我们已经介绍如何购买服务器。

## 进入ECS控制台

1. 进入控制台后，点击找到自己的购买的服务器，如果找不到图中序号1、2、3可以帮到你找到。找到后，`复制`公网IP地址，在浏览器地址中输入公网IP地址。（序号4第一行8.140.28.226），如`图1`：
   
    ![11](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-10.png "图1")
    <center>图1 ECS控制台</center>
2. 点击`远程连接`（步骤1序号5的位置），弹出弹窗后，选择蓝色`立即登录`按钮，如`图2`：

    ![2](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-11.png "图2")
    <center>图2 远程连接</center>
    
3. 输入您购买服务器时自定义的密码，如`图3`:
   
   ![3](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-12.png "图3")
    <center>图3 登录</center>
4. 登录成功，如`图4`，直接输入代码：
   ```bsh
   cat default.pass
   ```
   得到数据库的用户名和密码（只能使用“右键 -> 复制 -> 复制选中文本”来复制密码）。：
    ```md
    注：里面有数据库的 root 权限，自动生成数据库名，FTP 权限。
    数据库的管理地址为：http://您的IP/phpmyadmin/
    Moodle 访问安装地址为：http://您的IP/install.php 
    ```
    ![4](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-13.png "图4")
    <center>图4 登录成功</center>

## 安装Moodle步骤

5. 在浏览器中输入公网IP地址,如`图5`：

    ![5](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-14.png "图5")
    <center>图5 输入公网IP地址</center>

6. 选择您想要的语言,这里以选择`简体中文`为例,如`图6`:
   
    ![6](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-15.png "图6")
    <center>图6 选择语言</center>

7. 点击`向后`，如`图7`：
   
    ![7](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-16.png "图7")
    <center>图7 确认路径</center>

8. 点击`向后`，如`图8`：
    
    ![8](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-17.png "图8")
    <center>图8 选择数据库驱动</center>

9. 点击`向后`，如`图9`：
    
    ![9](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-18.png "图9")
    <center>图9 数据库设置</center>

10. 点击`继续`，如`图10`：
    
    ![10](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-19.png "图10")
    <center>图10 版权声明</center>

11. 点击`继续`，如`图11`：
    
    ![11](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-20.png "图11")
    <center>图11 安装拓展</center>
    
12. 点击`继续`，如`图12`：
    
    ![12](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-21.png "图12")
    <center>图12 安装成功</center>
    
13. 点击`继续`，按要求填好密码等参数，如`图13`：
    
    ![13](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-22.png "图13")
    <center>图13 填写信息</center>

14. 点击`保存更改`，，如`图14`：
    
    ![14](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-23.png "图14")
    <center>图14 保存更改</center>


## 软件安装路径

|软件件名称|路径地址|
|---|---|
|默认站点 www 根目录| /yjdata/www/wwwroot|
|Apache2.2.15 |/etc/httpd|
|php5.6 |/usr/local/php|
|mysql5.5 |/var/lib/mysql |
|Moodle3.1.2 |/yjdata/www/wwwroot|

## 常用命令

服务 启动，停止，重启操作

mysql: service mysqld (start|stop|restart)

vsftpd: service vsftpd (start|stop|restart)

httpd: service httpd (start|stop|restart