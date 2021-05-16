---
title: Hexo之音乐播放器
categories: Hexo
tags: [ Hexo , music , 插件 ]
date: 2021-05-16 21:27:24
updated: 2021-05-16 21:27:24
---

## 示例

### 普通音频

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=381912&auto=0&height=66"></iframe>

### 音乐网站

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=381962&auto=0&height=66"></iframe>

## 安装

使用`npm`来安装`hexo-tag-aplayer`:

```
npm install --save hexo-tag-aplayer
```

## 依赖

- APlayer.js > 1.8.0
- Meting.js > 1.1.1

## 使用

```

{% aplayer title author url [picture_url, narrow, autoplay, width:xxx, lrc:xxx] %}

```

### 标签参数

- `title`: 曲目标题
- `author`: 曲目作者
- `url`: 音乐文件 URL 地址
- `picture_url`: (可选) 音乐对应的图片地址
- `narrow`: （可选）播放器袖珍风格
- `autoplay`: (可选) 自动播放，移动端浏览器暂时不支持此功能
- `width: xxx`: (可选) 播放器宽度 (默认: 100%)
- `lrc:xxx`: （可选）歌词文件 URL 地址

当开启 `Hexo` 的 `文章资源文件夹` 功能时，可以将图片、音乐文件、歌词文件放入与文章对应的资源文件夹中，然后直接引用：

```
{% aplayer "Caffeine" "Jeff Williams" "caffeine.mp3" "picture.jpg" "lrc:caffeine.txt" %}
```
### 歌词标签

除了使用标签 `lrc` 选项来设定歌词，你也可以直接使用 `aplayerlrc` 标签来直接插入歌词文本在博客中：

```
{% aplayerlrc "title" "author" "url" "autoplay" %}
[00:00.00]lrc here
{% endaplayerlrc %}
```

### 播放列表

```
{% aplayerlist %}
{
    "narrow": false,                          // （可选）播放器袖珍风格
    "autoplay": true,                         // （可选) 自动播放，移动端浏览器暂时不支持此功能
    "mode": "random",                         // （可选）曲目循环类型，有 'random'（随机播放）, 'single' (单曲播放), 'circulation' (循环播放), 'order' (列表播放)， 默认：'circulation'
    "showlrc": 3,                             // （可选）歌词显示配置项，可选项有：1,2,3
    "mutex": true,                            // （可选）该选项开启时，如果同页面有其他 aplayer 播放，该播放器会暂停
    "theme": "#e6d0b2",                       // （可选）播放器风格色彩设置，默认：#b7daff
    "preload": "metadata",                    // （可选）音乐文件预载入模式，可选项： 'none' 'metadata' 'auto', 默认: 'auto'
    "listmaxheight": "513px",                 // (可选) 该播放列表的最大长度
    "music": [
        {
            "title": "CoCo",
            "author": "Jeff Williams",
            "url": "caffeine.mp3",
            "pic": "caffeine.jpeg",
            "lrc": "caffeine.txt"
        },
        {
            "title": "アイロニ",
            "author": "鹿乃",
            "url": "irony.mp3",
            "pic": "irony.jpg"
        }
    ]
}
{% endaplayerlist %}
```

### MeingJS 支持 (3.0 新功能)

<a href="https://github.com/metowolf/MetingJS">`MetingJS`</a>是基于 <a href="https://github.com/metowolf/Meting">`Meting API`</a> 的 `APlayer` 衍生播放器，引入 `MetingJS` 后，播放器将支持对于 QQ音乐、网易云音乐、虾米、酷狗、百度等平台的音乐播放。

如果想在本插件中使用 MetingJS，请在 `Hexo` 配置文件 `_config.yml` 中设置：

```
aplayer:
  meting: true
```
接着就可以通过 `{ % meting ... % }` 在文章中使用 MetingJS 播放器了：
```
<!-- 简单示例 (id, server, type)  -->
{% meting "60198" "netease" "playlist" %}

<!-- 进阶示例 -->
{% meting "60198" "netease" "playlist" "autoplay" "mutex:false" "listmaxheight:340px" "preload:none" "theme:#ad7a86"%}
```

有关 `{ % meting % }` 的选项列表如下:

|选项|默认值|描述|
|---|---|---|
|id|必须值|歌曲id / 播放列表 id / 相册 id / 搜索关键字|
|server|必须值|音乐平台: netease, tencent, kugou, xiami, baidu|
|type|必须值|song, playlist, album, search, artist|
|fixed|false|开启固定模式|
|mini|false|开启迷你模式|
|loop|all|列表循环模式：all, one,none|
|order|list|列表播放模式： list, random|
|volume|0.7|播放器音量|
|lrctype|0|歌词格式类型|
|listfolded|false|指定音乐播放列表是否折叠|
|storagename|metingjs|LocalStorage 中存储播放器设定的键名|
|autoplay|true|自动播放，移动端浏览器暂时不支持此功能|
|mutex|true|该选项开启时，如果同页面有其他 aplayer 播放，该播放器会暂停|
|listmaxheight|340px|播放列表的最大长度|
|preload|auto|音乐文件预载入模式，可选项： none, metadata, auto|
|theme|#ad7a86|播放器风格色彩设置|
|||

关于如何设置自建的 `Meting API` 服务器地址，以及其他 `MetingJS` 配置，请参考本章节`自定义配置`。

### PJAX 兼容

若在 Hexo 中使用了 PJAX，可能需要自己手动清理 APlayer 全局实例：

```
$(document).on('pjax:start', function () {
    if (window.aplayers) {
        for (let i = 0; i < window.aplayers.length; i++) {
            window.aplayers[i].destroy();
        }
        window.aplayers = [];
    }
});
```
## 自定义配置（3.0 新功能）

现在你可以在 Hexo 配置文件 _config.yml 中配置本插件：
```
aplayer:
  script_dir: some/place                        # Public 目录下脚本目录路径，默认: 'assets/js'
  style_dir: some/place                         # Public 目录下样式目录路径，默认: 'assets/css'
  cdn: http://xxx/aplayer.min.js                # 引用 APlayer.js 外部 CDN 地址 (默认不开启)
  style_cdn: http://xxx/aplayer.min.css         # 引用 APlayer.css 外部 CDN 地址 (默认不开启)
  meting: true                                  # MetingJS 支持
  meting_api: http://xxx/api.php                # 自定义 Meting API 地址
  meting_cdn: http://xxx/Meing.min.js           # 引用 Meting.js 外部 CDN 地址 (默认不开启)
  asset_inject: true                            # 自动插入 Aplayer.js 与 Meting.js 资源脚本, 默认开启
  externalLink: http://xxx/aplayer.min.js       # 老版本参数，功能与参数 cdn 相同
```

## 故障排除

### 标签参数空格问题

在 `Hexo` 标签中，用户可能无法直接在标签参数中加入空格

如果遇到这类问题，请直接将参数用双引号括起来使用，如下所示：

```
{% aplayer "Caffeine" "Jeff Williams" "caffeine.mp3" "autoplay" "width:70%" "lrc:caffeine.txt" %}
```

### 重复载入 Aplayer.js 资源脚本问题

本插件通过 `after_render:html`过滤器 , 将 `APlayer.js` 和 `Meting.js` 插入到使用了本插件标签 的 `HTML` 文件中:

```
<html>
  <head>
    ...
    <script src="assets/js/aplayer.min.js"></script>
    <script src="assets/js/meting.min.js"></script>
  </head>
  ...
</html>
```

但是 `after_render:html` 在一些情形下可能无法被正常触发:

- <a href="https://github.com/hexojs/hexo-inject/issues/1">Does not work with hexo-renderer-jade</a>
  
- `after_render:html` 似乎在 `Hexo` 服务器模式默认配置中无法被调用 (`hexo server`), 遇到这种情况用户可能需要使用 `hexo-server` 的静态文件解析模式 ( `hexo server -s`) 。
  
- 如果在博客生成过程中，插件发现 `after_render:html` 没有被调用，那么插件将会通过 `after_post_render` 过滤器来植入脚本。但是使用 `after_post_render` 会有重复载入 `APlayer.js` 的情况（例如当一个页面中存在多篇博客时），以及一些非文章页面将无法使用本插件。

如果想完全解决这个问题，用户可能需要自己在主题文件中手动加入 `Aplayer.js` 与 `Meting.js`，同时关闭插件的自动脚本插入功能：

```
aplayer:
  asset_inject: false
```
