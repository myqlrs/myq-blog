---
title: my first blog
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
  - 第一
  - first
---

## hexo
新建文章
`hexo new post <md文件名>`

每次我们添加或修改完本地文件后，使用：

```bash
hexo clean && hexo g -d
```

即可重新生成项目文件，并推送到 github 项目的 gh-pages 分支，为了备份数据，也方便我们在不同设备上进行编辑，最好将我们修改的文件推送到 master 分支进行保存：

```bash
git checkout master
git add .
git commit -am "myqlrs"
git push
```
