---
title: 小程序开发总结和心得
categories: 微信小程序
tags: [小程序资源]
date: 2021-08-14
updated: 2021-08-14

---

## 前言


最近学习和开发的重心一直在小程序，最近写了一款微信小程序项目，对小程序开发的流程及相关技术相对比较熟悉，在开发过程中也总结了一些心得经验、了解一些小程序文档上没有的东西、踩了一些坑。所以想着写篇文章记录下来，并借此将小程序开发的相关知识进行梳理，方便以后参考，也作为自己工作的阶段性总结。同时也希望可以通过文章，结识更多朋友，多交流，互相学习，共同进步。另文章若有不对之处，还望指出与不吝赐教。

本文主要记录一下初次开发小程序一些注意事项，小程序开发语言，对小程序的审核要求，小程序的特点，开发特点，

## 注意事项

### 类目：
- 小程序分为不得超出小程序类目范围，不得与其他小程序相似，不得与微信功能相似，如微信群聊，发红包等。
- 小程序所提供的所有服务类目功能，必须在小程序首页得到体现，要求清晰的分辨出主要功能，弱化干扰项，次要内容（呃呃呃呃，其实有时候卡的不是特别严，可以通过一定手段来规避）。
- 小程序大部分API和组件均属前端范畴，但是前端开发的第三方可能支持前端但不支持小程序，比如bootstrap，网页端的docment方法小程序中不能使用。

- 通过编写javascript/(w)xml/css实现逻辑，即开发语言要求了解：js;html;css

- - css 教程

- - 小程序教程

### 文件配置 

- .js后缀的是脚本文件，.json后缀的文件是配置文件，.wxss后缀的是样式表文件

- 微信小程序的语音格式是silk

- 微信为小程序提供的开发框架为MINA框架，类似于Weex,Vue框架

- app.json不能添加任何注释

- 每个页面的“路径+页面名”都要写到app.json 的pages中

- 所有页面层级不能超过5层

- 顶部navigation bar上的icon无法自定义（甚至目前”…”里面的操作也不能自定义）

- 顶部tab项不得超过4项（具体其实也得看是什么样子的项目了）

心得体会：纯html/css效率不高，标签写起来费时间，能可视化拖动自动生成，微信官方提供的开发工具就可以实现，但是用起来一卡一卡的，每次都会热加载就很不爽。前端UI框架可以考虑color UI 和 VantUI两套UI，这两套 UI 框架都是基于 VUE.js 实现的，对于新手开发者比较友好，网上开源项目也挺多的，这里就不讲了。

### 开发者工具

开发者工具有很多，如果你觉得自己能行，你还可以用记事本开发。

- VSCode（有小程序的插件可以用）

- 微信开发工具，调试方法（单步调试，数据查看，）等。

### 其他

入门Weex前需要了解一下知识，这样能帮助你更快的掌握
- Node：Node.js 教程
- Vue：《Vue.js官方教程》
- ES6：《ECMAScript 6 入门》
- Vue是一套构建用户界面的渐进式框架

小程序更新很快，基本直接看文档加度娘就可以上手了，开发工具也几乎几天一个版本。小程序的翻译:Mini Programes


## 标签

1. view
   
   和div差不多

2. text
   展示文字最好用这个，其他标签都不支持长按复制

3. icon
   
   icon可以直接用微信组件默认的图标，默认是iconfont格式的
   自定义的icon推荐svg sprite格式或者iconfont

4. input
   
   input 的类型，有效值：text, number, idcard, digit, time, date 。
   
   input不需要设置line-height或padding来纵向居中，默认placeholder的文字是居中的。
   
   小程序把checkbox和radio都单独做成了组件，默认的input只支持输入文本。
   
   上传文件在小程序里需要调用chooseImage事件完成;
   
   小程序CSS里的 :focus 不生效，需要修改placehoder的样式，通过placeholder-class=”class”来定义。
```
.login .input-group input::-webkit-input-placeholder {
color: #c0c0c0;
}
.login .input-group input:focus::-webkit-input-placeholder {
color: transparent;
}
```

5. picker
   
   picker默认支持普通、日期和时间三种选择器。

   picker通过bindchange事件来调取range中自定义的数据数据，并展示到页面中，调用的是系统原生的select。

   这里小程序废弃了select组件，考虑到的是这个组件的交互不适合移动场景，最终用picker代替了。

        选择 北京 上海

```
{{area[index]}}
Page({
data: {
area: [‘中国’, ‘美国’, ‘巴西’, ‘日本’],
}
})
```

6. navigator
   
   navigator支持相对路径和绝对路径的跳转，默认是打开新页面，当前页面打开需要加redirect;

   navigator仅支持5级页面的跳转;

   navigator不可跳转到小程序外的链接地址;

   登录页

   在小程序开发工具里，默认打开新页面，工具左上角有返回按钮。加上redirect，当前页打开，不出现返回按钮。

7. image
   
   小程序的image与HTML5的img最大的区别在于：小程序的image是按照background-image来实现的。
   
   默认image的高宽是320*240。必须通过样式定义去覆盖这个默认高宽，auto在这里不生效。(开发者说这样设置的原因是：如果设置 auto ，页面布局会因为图片加载的过程有一个闪的现象(例如高度从 0 到 height )，所以要求一定要设置一个宽度和高度。)
   
   最新的api支持获取图片的高宽。不过这里返回的高宽是px单位，不支持屏幕自适应;
   
   图片包括三种缩放模式scaleToFill、aspectFit、aspectFill和9种裁剪模式，三种缩放模式的实现原理对应如下：
```
   scaleToFill{
       background-size:100% 100%;//不保持纵横比缩放图片，使图片的宽高完全拉伸至填满 image 元素
}
aspectFit{
background-size:contain;//保持纵横比缩放图片，使图片的长边能完全显示出来。也就是说，可以完整地将图片显示出来。
}
aspectFill{
background-size:cover;//保持纵横比缩放图片，只保证图片的短边能完全显示出来。也就是说，图片通常只在水平或垂直方向是完整的，另一个方向将会发生截取。
}
```

## 组件

### 视图容器

- view 视图容器
- scroll-view 可滚动视图容器
- swiper 可滑动的视图容器
- share-element 共享元素
- page-container 页面容器
- movable-view 可移动的视图容器
- movable-area 可移动区域
- match-media  匹配检测节点
- cover-view 覆盖在原生组件之上的文本视图
- cover-image 覆盖在原生组件之上的图片视图。

### 基础内容

- icon 图标
- text 文字
- progress 进度条
- rich-text 富文本

### 表单组件(Form)

- button 按钮
- form 表单
- input 输入框
- checkbox 多项选择器
- radio 单项选择器
- picker 列表选择器
- slider 滑动选择器
- switch 开关选择器
- label 标签
- editor 富文本编辑器
- 

### 操作反馈组件(Interaction)

action-sheet 上拉菜单
modal 模态弹窗
progress 进度条
toast 短通知

### 导航(Navigation)

- navigator 应用内跳转
- functional-page-navigator 仅在插件中有效，用于跳转到插件功能页

### 多媒体(Media)

- audio 音频
- image 图片
- video 视频
- camera 系统相机
- live-player 实时音视频播放
- live-pusher 实时音视频录制

### 地图(Map)

- map 地图

### 画布(Canvas)

- canvas 画布

### 事件

- bindtap 点击事件
- touchstart 手指触摸动作开始
- touchmove 手指触摸后移动
- touchcancel 手指触摸动作被打断，如来电提醒，弹窗
- touchend 手指触摸动作结束
- tap 手指触摸后马上离开
- longtap 手指触摸后，超过350ms再离开

### 样式绑定

<view style="color:{{color}};" />

1

### 数据绑定

<view>{{id}}</view>
this.setData({
	"id": 5
});

1

2

3

4

控制多个视图容器

<block wx:if="{{true}}">
  <view> view1 </view>
  <view> view2 </view>
</block>
1

2

3

4

cover-image使用注意事项：
使用图片尽量使用image标签，使用cover-image标签无法改变z-index，默认图片是在最上层显示。image标签和cover-image使用方法也不太一样。image是在图片上设置宽高，cover-image是在外层容器上设置宽高

## 云开发

微信云开发提供了多个基础能力，包括数据库、云函数、云存储、云调用、HTTP API 。除此之外，云开发还提供了一系列的拓展能力，最近一直在做的就是一个内容管理的拓展能力的应用。

### 数据库

云开发提供了一个 JSON 数据库，顾名思义，数据库中的每条记录都是一个 JSON 格式的对象。一个数据库可以有多个集合（相当于关系型数据中的表），集合可看做一个 JSON 数组，数组中的每个对象就是一条记录，记录的格式是 JSON 对象。

关系型数据库和 JSON 数据库的概念对应关系如下表：


|关系型|文档型|
|---|---|
|数据库 database|	数据库 database|
|表 table|集合 collection|
|行 row	|记录 record / doc|
|列 column|	字段 field|

### 云存储

云开发提供了一块存储空间，提供了上传文件到云端、带权限管理的云端下载能力，开发者可以在小程序端和云函数端通过 API 使用云存储功能。

在小程序端可以分别调用 wx.cloud.uploadFile 和 wx.cloud.downloadFile 完成上传和下载云文件操作。下面简单的几行代码，即可实现在小程序内让用户选择一张图片，然后上传到云端管理的功能：

### 云函数

云函数是一段运行在云端的代码，无需管理服务器，在开发工具内编写、一键上传部署即可运行后端代码。

小程序内提供了专门用于云函数调用的 API。开发者可以在云函数内使用 wx-server-sdk 提供的 getWXContext 方法获取到每次调用的上下文（appid、openid 等），无需维护复杂的鉴权机制，即可获取天然可信任的用户登录态（openid）。

### 云调用


云调用是云开发提供的基于云函数使用小程序开放接口的能力，支持在云函数调用服务端开放接口，如发送模板消息、获取小程序码等操作都可以在云函数中完成，

### HTTP API

云开发资源也可以通过 HTTP 接口访问，即在小程序外访问，
