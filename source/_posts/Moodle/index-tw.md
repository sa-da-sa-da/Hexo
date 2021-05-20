---
title: 云服务器的购买（图文版）
categories: 云服务器
tags: [Moodle,ECS]
date: 2021-05-18
updated: 2021-05-18
---

## 子账户账号密码
已经为大家创建了阿里云的子账号。之所以不让大家直接注册阿里云账号，是因为如果注册了新用户的时间只有几个月，几个月内很多产品体验优惠非常大，一旦过了新用户的时间段，那么价格相应的会提高。
阿里云子账号：姓名每个字的首字母@serverlessframe.onaliyun.com
阿里云子账号密码统一为：qq123456，登陆后无需更改。
用户登录地址：https://signin.aliyun.com/serverlessframe.onaliyun.com/login.htm
实验过程中请大家节约不必要的开支，毕竟都是钱哇。




----

### 一、登录账号

1. 打开网页
   在浏览器地址栏输入用户登录地址（或者按住CTRL键+鼠标左击登录地址）：https://signin.aliyun.com/serverlessframe.onaliyun.com/login.htm
   
   如`图1`：在 @serverlessframe.onaliyun.com前输入姓名每个字的首字母并点击下一步。例如李政隆的子账户就是：lzl@serverlessframe.onaliyun.com。

2. 输入密码：qq123456，点击`登录`按钮。如 `图1`：
  
    ![1](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-1.png "图1")
    <center>`图1 登陆界面`</center>

3. 进入控制台后，点`云服务器ECS`按钮。如`图2`：
![2](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-2.png "图2")
   <center>`图2 阿里云控制台`</center>

### 二、创建云服务器

4. 进入云服务器ECS控制台后，直接点`创建实例`。如`图3`：
    ![3](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-3.png "图3")
   <center>`图3 ECS控制台`</center>

5. 基础配置，如`图4`：
   
    （1）在这里我们选择使用`自定义购买`的方式。

    （2）在`付费模式`中，我们选择`抢占式实例`。（便宜，极其便宜）

    （3）在`地域及可用区`中，我们选择`华北2（北京）`地区`随机分配`，当然也可以选择其他的地区，选择北京的原因主要是因为距离我们较近，且价格相对便宜，配置选择更多样；
    其次，也可以不选择随机分配，选择其他可用区，这个主要是以后的VPC内网络资源交换更方面迅捷，在阿里内网中传输数据不收费！

    （4）在`实例规格`中，我们如图`筛选`出来`1vCPU``2GiB`这里我们选择最便宜的一种`突发性能t5`实例规格。【注意】不要勾选`打开突发性能实例无性能约束模式`！

    （5）在`抢占式实例使用时长`，我们选择`无确定使用时长`选择该选项的。

    （6）在`单台实例规格上限价`，我们输入市场价格区间的最小值即可，如图中0.024

    （7）在 `镜像`中，选择`镜像市场`，搜索输入`moodle`，选择不收费且使用人数较多的即可。

    （8）该页面，其他地方建议不要动。

    （9）确定完以后，点击“下一步网络与安全组”，蹦出弹窗点击确认即可。

    ![4](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-4.png "图4")
    <center>`图4 基础配置`</center>
    
6. 网络与安全组,如`图5`
   
    （1）在`网络`中选择`test_vpc……`即可，随便选择。

    （2）在`公网 IP`中勾选`分配IPv4地址`。

    （3）在`带宽计费模式`选择`按使用流量`带宽峰值`5Mbps`即可，不用太大。

    （4）在`安全组`中选择安全组`jiaoji_1801_moodle / sg-2zejccy39e1gjlwq2etc`

    （5）其他不用更改。直接点击下一步`系统配置`。

    ![5](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-5.png "图5")
    <center>`图5 网络与安全组`</center>

7. 系统配置，如`图6`：
   
    （1）在“登陆凭证”中选择“自定义密码”
    
    （2）在“登录密码”中输入你的“登录密码”、输入“确认登录密码”并牢记你的密码。

    （3）实例名称修改成自己的名字或者唯一的昵称。主要是方便一会自己使用。
    ![6](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-6.png "图6")
    <center>图6 系统配置</center>

8. 分组设置（建议设置），如`图7`：
   
    建议如图设置：选择`用户`，并在后面输入自己的名字或者唯一的昵称。
    还是为了方便自己使用自己购买的服务器。
    ![7](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-7.png "图7")
    <center>图7 分组设置</center>

9.  确认订单，如`图8`：

    如`图8`所示：勾选`服务协议` <b>（必选）</b>，然后`创建实例`即可。就这样，我们就购买了一台云服务器。
    ![8](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-8.png "图8")
    <center>图8 确认订单</center>

10. 创建成功。点击1或者2，进入管理控制台，如`图9`：

    ![9](https://asdasdada.oss-cn-hongkong.aliyuncs.com/images/t-9.png "图9")
    <center>图9 创建成功</center>











