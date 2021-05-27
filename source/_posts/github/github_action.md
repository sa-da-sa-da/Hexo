---
title: 如何使用 GitHub Actions 快速构建流程
categories: 工作流
tags: Github Actions
date: 2021-05-28
updated: 2021-05-28
---

官网文档地址：https://docs.github.com/cn/actions

## GitHub Actions 是干什么用的？

具体的解释可以参照官网的说明，我这里举两个例子。

`场景1`： 一般的项目都有迭代不同的版本，但总会有一个 master 或 dev/pro 的版本做为最新版，一般的情况下，我们会在github上开不同的分支来管理，然后用版本号来区分，如下面这种：

![1](https://img2020.cnblogs.com/blog/1145671/202008/1145671-20200827141504658-173384996.png)

但我们每完成一个固定的版本都需要手动更新到 master 或 dev 分支上，甚至我想再开一个仓库去存放最新版本，那我们也每次都得手动 push 更新。

不过，这时候我们就可以用 GitHub Actions，选择我们要触发的时机（ pull 还是 push ？以及在哪个分支操作），然后代码上传就可以自动同步。

`场景2`：GitHub Pages 是 GitHub 提供给我们通过静态页构建博客的功能，但我们每次都需要本地打包生成静态页然后上传，不过有了 Actions 就可以把打包以及上传的动作交给GitHub ，我们只需要新增和更新文章然后同步到 GitHub 即可，甚至都可以直接在 GitHub 仓库中直接修改文章都可以。

 

`总结`：上面两个例子中，知道通过 Actions 可以上传，可以编译打包。不过，我们也可以用 Actions 做自动化测试，只要有相应的脚本就可以实现。

`拓展`：除了国外的 Actions ，国内有阿里云的云效、Coding、Gitee Go、华为云的 DevOps等。

## Github Actions 

GitHub Actions 帮助开发者自动完成软件开发周期内的任务。 GitHub Actions 是事件驱动的，意味着开发者可以在指定事件发生后运行一系列命令。例如，每次有人为仓库创建拉取请求时，都可以自动运行命令来执行软件测试脚本。

![2](https://docs.github.com/assets/images/help/images/overview-actions-simple.png)

此示意图说明如何使用 GitHub Actions 自动运行软件测试脚本。事件会自动触发其中包作业的工作流程。然后，作业使用步骤来控制操作运行的顺序。这些操作是自动化软件测试的命令。


简单地说，Actions就相当于Github给你提供了一个linux，你可以在里面上执行脚本。

其实 Github 只是给你创建了一个镜像，供你去执行脚本和命令，等你执行完了镜像会被删除。

## GitHub Actions 工作组件

!(3)[https://docs.github.com/assets/images/help/images/overview-actions-design.png]

### 工作流程

工作流程是添加到仓库的自动化过程。工作流程由一项或多项作业组成，可以计划或由事件触发。工作流程可用于在 GitHub 上构建、测试、打包、发布或部署项目。

### 事件

事件是触发工作流程的特定活动。例如，当有推送提交到仓库或者创建议题或拉取请求时，GitHub 就可能产生活动。还可以使用仓库分发 web 挂钩在发生外部事件时触发工作流程。

### Jobs

作业是在同一运行服务器上执行的一组步骤。 默认情况下，包含多个作业的工作流程将同时运行这些作业。 您也可以配置工作流程按顺序运行作业。 例如，工作流程可以有两个连续的任务来构建和测试代码，其中测试作业取决于构建作业的状态。 如果构建作业失败，测试作业将不会运行。

### 步骤

步骤是可以在作业中运行命令的单个任务。步骤可以是操作或 shell 命令。作业中的每个步骤在同一运行器上执行，可让该作业中的操作互相共享数据。

### 操作

操作是独立命令，它们组合到步骤以创建作业。操作是工作流程最小的便携式构建块。您可以创建自己的操作，也可以使用 GitHub 社区创建的操作。要在工作流程中使用操作，必须将其作为一个步骤。

### 运行器

运行器是安装了 `GitHub Actions` 运行器应用程序的服务器。您可以使用 GitHub 托管的运行器，也可以托管您自己的运行器。运行器将侦听可用的作业，每次运行一个作业，并将进度、日志和结果报告回 GitHub。GitHub 托管的运行器基于 `Ubuntu Linux`、`Microsoft Windows` 和 `macOS`，并且工作流程中的每个作业都在新的虚拟环境中运行。

## 创建示例工作流程

GitHub Actions 使用 YAML 语法来定义事件、作业和步骤。这些 YAML 文件存储在代码仓库中名为 .github/workflows 的目录中。

开发者在仓库中创建示例工作流程，只要推送代码，GitHub Actions 都将自动触发一系列命令。在此工作流程中，GitHub Actions 检出推送的代码，安装软件依赖项，并运行 `-v`。

1. 在仓库中创建 `.github/workflows/` 目录来存放工作流程文件。

2. 在 `.github/workflows/` 目录中，创建一个名为 `learn-github-actions.yml` 的新文件并添加以下代码。

```yaml
name: learn-github-actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
      - run: npm install -g bats
      - run: bats -v
```

3. 提交这些更改并将其推送到 GitHub 仓库。


## 配置工作流文件

如何使用 YAML 语法来创建工作流程文件，本节解释介绍示例的每一行：

|||
|---|---|
|`name: learn-github-actions`|可选 - 将出现在 GitHub 仓库的 Actions（操作）选项卡中的工作流程名称。|
|`on: [push]`|指定自动触发工作流程文件的事件。 此示例使用 push 事件，这样每次有人推送更改到仓库时，作业都会运行。 您可以设置工作流程仅在特定分支、路径或标记上运行。 有关包含或排除分支、路径或标记的语法示例，请参阅“GitHub Actions 的工作流程语法”。|
|`jobs:`|将 learn-github-actions 工作流程文件中运行的所有作业组合在一起。|
|`check-bats-version:`|定义存储在 jobs 部分的 check-bats-version 作业的名称。|
|  `runs-on: ubuntu-latest`|配置作业在 Ubuntu Linux 运行器上运行。 这意味着该作业将在 GitHub 托管的新虚拟机上执行。 有关使用其他运行器的语法示例，请参阅“GitHub Actions 的工作流程语法”。|
|`  steps:`|将 check-bats-version 作业中运行的所有步骤组合在一起。 此部分下嵌套的每项都是一个单独的操作或 shell 命令。|
|`    - uses: actions/checkout@v2`|uses 关键字指示作业检索名为 actions/checkout@v2 的社区操作的 v2。 这是检出仓库并将其下载到运行器的操作，允许针对您的代码运行操作（例如测试工具）。 只要工作流程针对仓库的代码运行，或者您使用仓库中定义的操作，您都必须使用检出操作。|
|`    - uses: actions/setup-node@v1`|此操作会在运行器上安装 node 软件包，使您可以访问 npm 命令。|
|`    - run: npm install -g bats`|run 关键字指示作业在运行器上执行命令。 在这种情况下，使用 npm 来安装 bats 软件测试包。|
|`    - run: bats -v`|最后，您将运行 bats 命令，并且带有输出软件版本的参数。

## 配置事件触发条件

开发者可以使用 on 工作流程语法配置工作流程为一个或多个事件运行。 

### 使用单一事件的示例

```yaml
# Triggered when code is pushed to any branch in a repository
on: push
```

### 使用事件列表的示例

```yaml
# Triggers the workflow on push or pull request events
on: [push, pull_request]
```

### 使用具有活动类型或配置的多个事件示例

如果需要为一个事件指定活动类型或配置，必须分别配置每个事件。必须为所有事件附加冒号 （` : < / 0），包括没有配置的事件。

```yaml
on:
  # Trigger the workflow on push or pull request,
  # but only for the main branch
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  # Also trigger on page_build, as well as release created events
  page_build:
  release:
    types: # This configuration does not affect the page_build event above
      - created
```

<div class = "warning"><blockquote>
<p>
注意：无法使用 GITHUB_TOKEN 触发新的工作流程。 更多信息请参阅“使用个人访问令牌触发新的工作流程”。
</p></blockquote>
</div>

### 安排的事件

`schedule` 事件允许您在计划的时间触发工作流程。

<div class="warning"><blockquote><p>
注意： `schedule` 事件在 GitHub Actions 工作流程运行期间负载过高时可能会延迟。 高负载时间包括每小时的开始时间。 为了降低延迟的可能性，将您的工作流程安排在不同时间运行。
</p></blockquote></div>


`计划`

|Web 挂钩事件有效负载|活动类型|GITHUB_SHA|GITHUB_REF|
|---|---|---|---|
|n/a|n/a|默认分支上的最新提交|默认分支|

您可以使用 `POSIX cron` 语法安排工作流程在特定的 `UTC` 时间运行。预定的工作流程在默认或基础分支的最新提交上运行。您可以运行预定工作流程的最短间隔是每 5 分钟一次。

此示例在每天 5:30 和 17:30 UTC 触发工作流程：

```yaml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

计划任务语法有五个字段，中间用空格分隔，每个字段代表一个时间单位。

```yaml
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of the month (1 - 31)
│ │ │ ┌───────────── month (1 - 12 or JAN-DEC)
│ │ │ │ ┌───────────── day of the week (0 - 6 or SUN-SAT)
│ │ │ │ │                                   
│ │ │ │ │
│ │ │ │ │
* * * * *
```

您可在这五个字段中使用以下运算符：


|运算符|描述|示例|
|---|---|---|
|*|任意值|* * * * * 在每天的每分钟运行。|
|,|值列表分隔符|2,10 4,5 * * * 在每天第 4 和第 5 小时的第 2 和第 10 分钟运行。|
|-|值的范围|0 4-6 * * * 在第 4、5、6 小时的第 0 分钟运行。|
|/|步骤值|20/15 * * * * 从第 20 分钟到第 59 分钟每隔 15 分钟运行（第 20、35 和 50 分钟）。|


<div class="warning"><blockquote><p>
注： GitHub Actions 不支持非标准语法 @yearly、@monthly、@weekly、@daily、@hourly 和 @reboot。
</p></blockquote></div>

## 查看作业的活动

作业开始运行后，就可以查看运行进度的可视化图以及查看 GitHub 上每个步骤的活动。



1. 在 GitHub 上，导航到仓库的主页面。

2. 在仓库名称下，单击 Actions（操作）。
![导航到仓库](https://docs.github.com/assets/images/help/images/learn-github-actions-repository.png)

3. 在左侧边栏中，单击您想要查看的工作流程。
![工作流程结果的屏幕截图](https://docs.github.com/assets/images/help/images/learn-github-actions-workflow.png)

4. 在“Workflow runs（工作流程运行）”下，单击您想要查看的运行的名称。
![工作流程运行的屏幕截图](https://docs.github.com/assets/images/help/images/learn-github-actions-run.png)

5. 在 Jobs（作业）下或可视化图中，单击您要查看的作业。
![选择作业](https://docs.github.com/assets/images/help/images/overview-actions-result-navigate.png)

6. 查看每个步骤的结果。
![工作流程运行详细信息的屏幕截图](https://docs.github.com/assets/images/help/images/overview-actions-result-updated-2.png)

