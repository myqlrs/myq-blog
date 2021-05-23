---
title: Aplayer搭配Metingjs音乐插件的使用
date: 2021-01-08 06:31:34
author: myqlrs
top: true
cover: true
categories:
  - blog
  - hexo
tags:
  - blog
  - hexo
keywords:
  - Aplayer
  - hexo
  - blog
  - Metingjs
---

## Aplayer 搭配 MetingJS 音乐插件的使用

### 1. Aplayer 和 MetingJ 的介绍

Aplayer 官网文档：<https://aplayer.js.org/#/>
Metingjs 官网文档：<https://github.com/metowolf/MetingJS>
Aplayer 是一个功能强大的 HTML5 音乐播放器，Metingjs 基于 Aplayer 插件封装好的插件，开箱即用。

### 2. MetingJS 的简单使用

MetingJS 支持 Aplayer 版本

| Version | API Status | APlayer |
| ------- | ---------- | ------- |
| 1.2.x   | Supported  | 1.10.0  |
| 2.0.x   | Latest     | 1.10.0  |

简单使用

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title></title>
    <!-- require APlayer -->
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css"
    />
    <script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
    <!-- require MetingJS -->
    <script src="https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js"></script>
  </head>
  <body>
    <meting-js server="netease" type="playlist" id="60198"></meting-js>
  </body>
</html>
```

> **解析**：
> server="netease" type="playlist" id="60198"

> server 指音乐平台，netease 指网易云音乐， type 类型，playlist 列表，id 指歌曲的 i 或者专辑或列表外链 id
> 因此重点在于指定平台，指定外链 id

中文版选项

| 选项                        | 默认     | 描述                                                                       |
| --------------------------- | -------- | -------------------------------------------------------------------------- |
| id(编号)                    | require  | 歌曲 ID /播放列表 ID /专辑 ID /搜索关键字                                  |
| server(平台)                | require  | 音乐平台：netease，tencent，kugou，xiami，baidu                            |
| type（类型）                | require  | song，playlist，album，search，artist                                      |
| auto（支持类种 类）         | options  | 音乐链接，支持：netease，tencent，xiami                                    |
| fixed（固定模式）           | false    | 启用固定模式，默认 false                                                   |
| mini（迷你模式）            | false    | 启用迷你模式,默认 false                                                    |
| autoplay（自动播放）        | false    | 音频自动播放，默认 false                                                   |
| theme(主题颜色)             | #2980b9  | 默认#2980b9                                                                |
| loop（循环）                | all      | 播放器循环播放，值：“all”，one”，“none”                                    |
| order(顺序)                 | list     | 播放器播放顺序，值：“list”，“random”                                       |
| preload(加载)               | auto     | 值：“none”，“metadata”，“'auto”                                            |
| volume（声量）              | 0.7      | 默认音量，请注意播放器会记住用户设置，用户自己设置音量后默认音量将不起作用 |
| mutex（限制）               | true     | 防止同时播放多个玩家，在该玩家开始播放时暂停其他玩家                       |
| lrc-type（歌词）            | 0        | 歌词显示                                                                   |
| list-folded（列表折叠）     | false    | 指示列表是否应该首先折叠                                                   |
| list-max-height（最大高度） | 340px    | 列出最大高度                                                               |
| storage-name（储存名称）    | metingjs | 存储播放器设置的 localStorage 键                                           |

### 3. 迷你版背景音乐

根据以上参数，写好了迷你版背景音乐，默认在左下角显示，默认列表折叠，默认不显示歌词

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title></title>
    <!-- require APlayer -->
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css"
    />
    <script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
    <!-- require MetingJS -->
    <script src="https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js"></script>
  </head>
  <body>
    <meting-js
      server="netease"
      type="playlist"
      id="60198"
      fixed="true"
      autoplay="true"
      loop="all"
      order="random"
      preload="auto"
      list-folded="ture"
      list-max-height="500px"
      lrc-type="1"
    >
    </meting-js>
  </body>
</html>
```

抽取出来

```html
<!--css-->
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css"
/>
<!--js-->
<script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js"></script>
<!--使用-->
<meting-js
  server="netease"
  type="playlist"
  id="60198"
  fixed="true"
  autoplay="true"
  loop="all"
  order="random"
  preload="auto"
  list-folded="ture"
  list-max-height="500px"
  lrc-type="1"
>
</meting-js>
```

### 4. 网易云音乐外链获取方法

1、打开网页版网易云音乐：<https://music.163.com/#>

2、选择想要添加到音乐列表去的音乐,生成外链，获取 id

### 5. 网易云音乐个人歌单 id 获取

登陆个人网易云音乐，打开歌单，查看浏览器地址，id 后面就是歌单

### 6. 博客园引用背景音乐

页脚区

```html
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css"
/>
<script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js"></script>
<div>
  <meting-js
    server="netease"
    type="playlist"
    id="60198"
    fixed="true"
    autoplay="true"
    loop="all"
    order="random"
    preload="auto"
    list-folded="ture"
    list-max-height="500px"
    lrc-type="1"
  >
  </meting-js>
</div>
```
