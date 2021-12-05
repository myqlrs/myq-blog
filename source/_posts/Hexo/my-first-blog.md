---
title: Hexo使用说明
date: 2021-01-08 06:13:29
author: myqlrs
img: /images/01.jpg
top: true
cover: true #是否加入首页轮播
coverImg: /images/01.jpg
toc: true # 目录
mathjax: false
summary: 这是你自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要
categories: Markdown
tags:
  - hexo
keywords:
  - Hexo
---

## hexo
新建文章
```bash
$ hexo clean #清除生成的博客静态文件
$ hexo g #生成博客静态文件=命令hexo generate
$ hexo s #启动博客的本地预览=命令hexo server
$ hexo d #推送博客到远程仓库=命令hexo deploy
$ hexo new page xxx #新建名为"xxx"的页面
$ hexo new xxx #新建名为"xxx"的文章
$ hexo d -g #生成静态博客并推送到Git远程仓库
$ hexo s -g #生成静态博客并启动本地预览
```
每次我们添加或修改完本地文件后，使用：
```bash
hexo clean && hexo g -d
```

即可重新生成项目文件，并推送到 github 项目的 gh-pages 分支，
为了备份数据，也方便我们在不同设备上进行编辑，最好将我们修改的文件推送到 master 分支进行保存：

```bash
git checkout master
git add .
git commit -am "myqlrs"
git push
```
