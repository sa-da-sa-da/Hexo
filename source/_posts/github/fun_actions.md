---
title: 如何使用 GitHub Actions + Funcraft 快速构建部署 Nextra
categories: 工作流
tags: [Github Actions,Serverless]
date: 2021-05-28
updated: 2021-05-28
---

## Funcraft 是什么？

Funcraft 是一个支持 Serverless 应用部署的工具，帮助开发者便捷地管理函数计算、API 网关、日志服务等资源。Funcraft通过一个资源配置文件 template.yml ，协助开发者进行开发、构建、部署等操作。Funcraft 主要功能：

1. 配置 Funcraft。在使用 fun 命令前需要先配置 Funcraft 文件， Funcraft 有三种配置方式：
- 通过 .env 文件配置：在 template.yml 文件同级目录下，创建一个 .env 文件，并录入以下配置：
  
```yml
ACCOUNT_ID=xxxxxxxx
REGION=cn-shanghai
ACCESS_KEY_ID=xxxxxxxxxxxx
ACCESS_KEY_SECRET=xxxxxxxxxx
FC_ENDPOINT=https://{accountid}.{region}.fc.aliyuncs.com
TIMEOUT=10
RETRIES=3
```

- 通过 fun config 命令配置：执行 `fun config` 命令，按照提示依次配置 Account ID、AccessKey ID、AccessKey Secret、Default Region Name。其中 Account ID、AccessKey ID 可以在函数计算控制台概览页的常用信息区域获得。

- 通过环境变量配置

2. fun init 初始化应用
3. 使用 fun local 进行本地调试
4. 安装第三方依赖
5. fun build 构建代码包
6. 使用 fun build 构建代码包
7. 使用 fun nas 管理NAS文件
8. 使用 fun deploy 部署应用
9. 部署 API 网关

总的来说，Funcraft 对开发者还是很友好的，使用起来非常方便。

## 为什么这么搞？

越来越多的人会把 GitHub 托管代码，当我们做网站页面，在本地也可以正常运行，通过本地的fun deploy也可以顺利部署，这个时候面临了新的问题：我如何更新我的网站？是否每次都要手动的在本地发布？是否可以通过 Github Actions + Funcraft 进行自动化部署呢？

本文将以 next.js 为示例进行部署。其他技术栈或者其他框架可以参考。

## 准备

1. 新建私有代码仓库，因为暂时不能通过环境变量来部署，只能通过录入 .env 文件来配置Funcraft；
2. 将 GitHub 仓库同步到 next.js 本地代码包；
3. 准备AK（ Account ID、AccessKey ID、AccessKey ）；

## 快速入门

1. 在 next.js 本地代码包中创建 template.yml ，录入以下配置：

```yml
ROSTemplateFormatVersion: '2015-09-01'
Transform: 'Aliyun::Serverless-2018-04-03'
Resources:
  Service: # service name
    Type: 'Aliyun::Serverless::Service'
    Properties:
      Description: This is FC service
    Function: # function name
      Type: 'Aliyun::Serverless::Function'
      Properties:
        Handler: index.handler
        Runtime: custom
        CodeUri: ./
        MemorySize: 128
        InstanceConcurrency: 5
        Timeout: 2
        
      Events:
        httpTrigger:
          Type: HTTP
          Properties:
            AuthType: ANONYMOUS
            Methods: ['GET', 'POST', 'PUT']
  Domain:
    Type: Aliyun::Serverless::CustomDomain
    Properties:
      DomainName: Auto# Your Domain
      Protocol: HTTP
      RouteConfig:
        Routes:
          "/*":
            ServiceName: Service
            FunctionName: Function
```

2. 在 next.js 本地代码包 template.yml 同级目录下创建 `.env` 文件，录入以下配置：

```env
ACCOUNT_ID=xxxxxxxx
REGION=cn-shanghai
ACCESS_KEY_ID=xxxxxxxxxxxx
ACCESS_KEY_SECRET=xxxxxxxxxx
FC_ENDPOINT=https://{accountid}.{region}.fc.aliyuncs.com
TIMEOUT=60
RETRIES=3
```
3. 在 next.js 本地代码包 template.yml 同级目录下创建 `bootstrap` 文件，录入以下配置：

```sh
#!/usr/bin/env bash
export PORT=9000
npx next start --port $PORT
```

4. 向 GitHub 仓库提交代码。
   
5. 创建 GitHub Actions 工作流，并录入以下配置：

```yml
name: next Publish

on:
  push:
    branches: [ main ]

jobs:
  publish-website:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: 10
          registry-url: https://registry.npmjs.org/
      - run: yarn install
      - run: yarn run build
      - run: npm install @alicloud/fun -g
      - run: chmod 777 bootstrap
      - run: fun deploy -y
```

6. 去 Actions 中查看我们的流程。因为我们在第四步配置的是自动化的流程，所以，直接就开始部署了。

## 步骤

### 创建私有代码仓库
   
创建代码仓库，并设置为私有。因为有机密信息，所以不建议公开。
   
### Git Clone

将代码仓库 git clone 到本地。
   
### 安装 Next.js
   
在本地仓库文件夹中安装 next.js。
   
### 创建文件
   
1. 在 next.js 本地代码包中创建 `template.yml` ，录入以下配置：

```yml
ROSTemplateFormatVersion: '2015-09-01'
Transform: 'Aliyun::Serverless-2018-04-03'
Resources:
  Service: # service name
    Type: 'Aliyun::Serverless::Service'
    Properties:
      Description: This is FC service
    Function: # function name
      Type: 'Aliyun::Serverless::Function'
      Properties:
        Handler: index.handler
        Runtime: custom
        CodeUri: ./
        MemorySize: 128
        InstanceConcurrency: 5
        Timeout: 2
        
      Events:
        httpTrigger:
          Type: HTTP
          Properties:
            AuthType: ANONYMOUS
            Methods: ['GET', 'POST', 'PUT']
  Domain:
    Type: Aliyun::Serverless::CustomDomain
    Properties:
      DomainName: Auto# Your Domain
      Protocol: HTTP
      RouteConfig:
        Routes:
          "/*":
            ServiceName: Service
            FunctionName: Function
```

<div class="sucess"><blockquote>
<p>备注：</p>
<p>1. <b>Service</b> 和<b>Funcion</b>的名字是可以更改的</p>
<p>2. <b>MemorySize</b>运行实例所需的内存规格，不需要设置太高，128 MB 就够加载静态页面用了，默认为弹性实例，最高内存规格为 3072MB，具体设置阶梯见<a href="https://help.aliyun.com/document_detail/179379.html?spm=a2c4g.11174283.6.545.20685212BZE9sm">文档</a>；<b>InstanceConcurrency</b>并发度设置为 5 即可，设置区间为0-100，具体见<a href="https://help.aliyun.com/document_detail/179379.html?spm=a2c4g.11174283.6.545.20685212BZE9sm">文档</a>；
<b>Timeout:</b> 超时时间设置为 2 秒，静态页面加载超过两秒也没必要搞了，看的人都跑光了！当然，除了业务真的长时间运行的需要。我个人都是设置为 1 秒。最大超时时间为 600 秒，也即函数运行最长时间为 600 秒。</p>
<p>3. 触发器选择http函数即可。自定义域名可以选择 Auto 和自己持有的域名。选择 Auto 方式可以用阿里云自动分配的一个域名，但该域名具有时效性和调用次数限制。自定义域名可以把 Auto 替换成自己的域名，也可以通过阿里云函数计算控制台搞定。但二者前提都要配置域名解析到您的Endpoint，Endpoint的CNAME的格式为<accountID>.<region>.fc.aliyuncs.com。例如，您的自定义域名为api.app.com，您的accountID为12345，地域为上海，则需要设置api.app.com的CNAME为12345.cn-shanghai.fc.aliyuncs.com；</p>
<p>4. template.yml 全量配置见<a href="https://github.com/alibaba/funcraft/blob/master/docs/specs/2018-04-03-zh-cn.md">文档<</a>/p>
</blockquote></div>

2. 在 next.js 本地代码包 template.yml 同级目录下创建 `.env` 文件，录入以下配置：

```env
ACCOUNT_ID=xxxxxxxx
REGION=cn-shanghai
ACCESS_KEY_ID=xxxxxxxxxxxx
ACCESS_KEY_SECRET=xxxxxxxxxx
FC_ENDPOINT=https://{accountid}.{region}.fc.aliyuncs.com
TIMEOUT=60
RETRIES=3
```
可以通过阿里云控制台获取，具体请见<a href="https://help.aliyun.com/document_detail/116401.html?spm=a2c4g.11174283.6.693.57f65662CbQ2x0">阿里云官方文档</a>，建议用 RAM 账户。

3. 在 next.js 本地代码包 template.yml 同级目录下创建 `bootstrap` 文件，录入以下配置：

```sh
#!/usr/bin/env bash
export PORT=9000
npx next start --port $PORT
```

这个是 Custom Runtime 运行环境必须的文件，且需要为 `bootstrap` 赋予777权限或者755权限。否则将会报错。这是因为 Serverless 函数都需要一个入口函数，而 Funcraft 的 next.js 组件直接会把next.js 部署到 Custome Runtime 环境下。而 Custome Runtime 环境没有入口函数，仅需要一个`bootstrap` 文件启动 http server 即可。

### 目录结构

```
├─pages          页面目录
│  ├─index.js       首页
│ 
├─public         公共文件
│  ├─favicon.ico           
│  ├─vercel.svg
│            
├─.env          环境变量
│ 
├─bootstrap       函数启动文件 
│ 
├─package.json      依赖文件
│
├─template.yml      fun 配置文件

```

### 提交代码
   
向 Github 仓库提交写好的代码。

### 创建 Actions

创建 Github Actions ，并录入以下配置：

```yml
name: next Publish

on:
  push:
    branches: [ main ]

jobs:
  publish-website:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: 10
          registry-url: https://registry.npmjs.org/
      - run: yarn install
      - run: yarn run build
      - run: npm install @alicloud/fun -g
      - run: chmod 777 bootstrap
      - run: fun deploy -y
```

此时我再`push`代码，就可以自动将网站发布出来了。

这里面的核心点：

- 安装依赖：  `run：yarn install`
- 打包代码：  `run：yarn run build `
- 安装Funcraft： `run: npm install @alicloud/fun -g`
- 赋予 bootstrap 文件 777 权限： `run：chmod 777 bootstrap`
- 部署：     `run: fun deploy -y`

部署效果：

![1](https://moodle-img-video.oss-cn-zhangjiakou.aliyuncs.com/sakaay/fc_actions_1.png)

页面效果：

![2](https://moodle-img-video.oss-cn-zhangjiakou.aliyuncs.com/sakaay/fc_actions_2.png)

<div class="warning"><blockquote><p>
这里要说明，此处配置密钥信息，不能使用 Github 的 Secrets 功能。那我们用的是什么呢？我们使用的是 <b>.env</b> 文件配置环境变量，这也就是为什么我们仓库要私有化的原因。
</p></blockquote></div>


## 总结

其实，通过这一套步骤下来，每天只需要搞搞代码，然后 `push` 一下就可以成功部署了，通过 GitHub Actions 和 Funcraft 走这一套还是比较方便的。得益于 Funcraft 的便捷，只需要两三行代码，就可以配置出 Github 的 CD 能力，将网站持续发出去，我觉得这个还是特别方便的；

当然，目前来看还是有一些问题等待去做的：

1. Funcraft 的场景还是有待丰富的；

2. Funcraft 其实尚未支持 CI/CD ，以致于我在尝试配置的过程中需要不断试错，最终试出来这样的一种方案，并分享出来；

3. 期待更多的方案。

