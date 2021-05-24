---
title: 阿里云函数计算 Thinkphp 在 PHP7.2 环境下的入口函数（2）
categories: [Thinkphp , Serverless]
tags: [ Thinkphp , Serverless , 插件 ]
date: 2021-05-24 
updated: 2021-05-24 

---

<div class="sucess">
<blockquote>
<p>
简介： 用阿里云函数计算部署thinkphp5.1
本文借鉴<a href="https://developer.aliyun.com/article/640912">十分钟上线-函数计算玩转 WordPress </a>。
演示地址：<a href="thinkphp.serverlessframe.com">thinkphp.serverlessframe.com</a>
</p>
</blockquote>
</div>

## 准备

1. tp6 源码

2. 登录阿里云函数计算控制台。

## 步骤

1. 后台创建 PHP7.2 环境的函数，创建http触发器，这些就不详细写了，可以去看<a href="">文档</a>。

2. 打开 `tp6` 的入口文件（/public/index.php）在 `namespace think;` 这句的下面，加如下代码：

```php
if(!$_SERVER['PATH_INFO']) $_SERVER['PATH_INFO'] = preg_replace("/^(\?s\=\/)/","",$_SERVER['REQUEST_URI']);
```

3. 准备 `tp6` 源码，在根目录创建`index.php`文件(作为函数的入口文件)，

目录结构如下：
```
├─app           应用目录
│  ├─controller      控制器目录
│  ├─model           模型目录
│  ├─ ...            更多类库目录
│  │
│  ├─common.php         公共函数文件
│  └─event.php          事件定义文件
│
├─config                配置目录
│  ├─app.php            应用配置
│  ├─cache.php          缓存配置
│  ├─console.php        控制台配置
│  ├─cookie.php         Cookie配置
│  ├─database.php       数据库配置
│  ├─filesystem.php     文件磁盘配置
│  ├─lang.php           多语言配置
│  ├─log.php            日志配置
│  ├─middleware.php     中间件配置
│  ├─route.php          URL和路由配置
│  ├─session.php        Session配置
│  ├─trace.php          Trace配置
│  └─view.php           视图配置
│
├─view            视图目录
├─route                 路由定义目录
│  ├─route.php          路由定义文件
│  └─ ...   
│
├─public                WEB目录（对外访问目录）
│  ├─index.php          入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于apache的重写
│
├─extend                扩展类库目录
├─index.php             函数计算入口函数文件
├─runtime               应用的运行时目录（可写，可定制）
├─vendor                Composer类库目录
├─.example.env          环境变量示例文件
├─composer.json         composer 定义文件
├─LICENSE.txt           授权说明文件
├─README.md             README 文件
├─think                 命令行入口文件
```

代码如下：

```php
<?php
#自定义的域名，绑定了自定义域名的，可以换成自己自定义的。
$MY_HOST    = "31199382.cn-beijing.fc.aliyuncs.com";
#web目录，默认是tp5的public目录，绝对路径，如：/code/public
$WWW_DIR = '/code/public';

function handler($request, $context){

    #如果不使用函数计算后台提供的那个厂厂的域名，这句可以注释掉。
    if(strpos($request->getAttribute("requestURI"),"/2016-08-15/proxy") !== false) $request = clearFcHost($request,$context);#兼容 fc后台的url地址

    $uri  = $request->getAttribute("requestURI");
    $file = explode("?", $uri)[0];
    if($file=='/') $uri='/';#
    $file = $GLOBALS['WWW_DIR'].$file;

    if(file_exists($file) and $uri!='/'){
        if(strpos($uri,".php")) return php_run(basename($file), $request, $context);#php_run
        return static_run($uri);#static_run
    }

    $request = $request->withAttribute("requestURI", "?s=".$uri);
    return php_run('index.php', $request, $context);# php_run

}

function php_run($name,$request, $context)
{
    return $GLOBALS['fcPhpCgiProxy']->requestPhpCgi($request, $GLOBALS['WWW_DIR'], $name,['SERVER_NAME' => $GLOBALS['MY_HOST'], 'SERVER_PORT' => '80', 'HTTP_HOST' => $GLOBALS['MY_HOST']],['debug_show_cgi_params' => false, 'readWriteTimeout' => 15000]);
}
use RingCentral\Psr7\Response;
function static_run($uri): Response{
    $file_dir = $GLOBALS['WWW_DIR'].$uri; #完整文件路径
    $file_dir = explode("?", $file_dir)[0]; #去掉动态路径
    if(is_dir($file_dir)) $file_dir .= '/index.html';# 可以这里定义目录的默认索引页
    $handle   = fopen($file_dir, "r");
    $contents = fread($handle, filesize($file_dir));
    fclose($handle);
    return new Response(200, ['Content-Type'  => $GLOBALS['fcPhpCgiProxy']->getMimeType($file_dir),'Cache-Control' => "max-age=8640000",'Accept-Ranges' => 'bytes'], $contents);
}

function clearFcHost($request,$context){
    $uri  = $request->getAttribute("requestURI");
    $uri  = str_replace("/2016-08-15/proxy/".$context['service']['name']."/".$context['function']['name'],"",$uri);
    $request = $request->withAttribute("requestURI", $uri);
    return $request;
}

#错误处理
function error($code) {
    #if($resp->getStatusCode() !=200) return error($resp->getStatusCode());
    return '错误';
}
```

4. 由于函数计算只有`/tmp`目录可写，所以要改一下 `tp6` 的配置文件。为什么？见<a href="https://help.aliyun.com/document_detail/181824.htm?spm=a2c4g.11186623.2.42.78956e6arU1B7M#concept-1919454">文章</a>

编辑`/config/cache.php`

```php
<?php

// +----------------------------------------------------------------------
// | 缓存设置
// +----------------------------------------------------------------------

return [
    // 默认缓存驱动
    'default' => env('cache.driver', 'file'),

    // 缓存连接方式配置
    'stores'  => [
        'file' => [
            // 驱动方式
            'type'       => 'File',
            // 缓存保存目录
            // 只要缓存在 /tmp 目录下就行了
            // 后面的 /cache名字是什么无所谓
            // 注意缓存保存时间较短
            'path'       => '/tmp/cache', 
            // 缓存前缀
            'prefix'     => '',
            // 缓存有效期 0表示永久缓存
            'expire'     => 0,
            // 缓存标签前缀
            'tag_prefix' => 'tag:',
            // 序列化机制 例如 ['serialize', 'unserialize']
            'serialize'  => [],
        ],
        // 更多的缓存连接
    ],
];

```

编辑`/config/log.php`

```php
<?php

// +----------------------------------------------------------------------
// | 日志设置
// +----------------------------------------------------------------------
return [
    // 默认日志记录通道
    'default'      => env('log.channel', 'file'),
    // 日志记录级别
    'level'        => [],
    // 日志类型记录的通道 ['error'=>'email',...]
    'type_channel' => [],
    // 关闭全局日志写入
    'close'        => false,
    // 全局日志处理 支持闭包
    'processor'    => null,

    // 日志通道列表
    'channels'     => [
        'file' => [
            // 日志记录方式
            'type'           => 'File',
            // 日志保存目录
            // 只要缓存在 /tmp 目录下就行了
            // 后面的 /log 名字是什么无所谓
            // 注意缓存保存时间较短
            'path'           => '/tmp/log',
            // 单文件日志写入
            'single'         => false,
            // 独立日志级别
            'apart_level'    => [],
            // 最大日志文件数量
            'max_files'      => 0,
            // 使用JSON格式记录
            'json'           => false,
            // 日志处理
            'processor'      => null,
            // 关闭通道日志写入
            'close'          => false,
            // 日志输出格式化
            'format'         => '[%s][%s] %s',
            // 是否实时写入
            'realtime_write' => false,
        ],
        // 其它日志通道配置
    ],

];

```

5. 接下来，就可以直接开始调试啦。
