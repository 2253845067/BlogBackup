---
title: Hexo建立一个独立的HTML页面
date: 2022-06-10
updated: 2022-06-10
description: Hexo建立一个独立的HTML页面，跳过渲染配置，保留原汁原味
tags:
  - Hexo
categories:
  - 教程
cover: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/OIP.jpg
---

Hexo 是一个轻量、快速，简单和强大的博客框架，当我们需要发布博文的时候，只需要将写好的文章（MarkDown文件）放入本地的 `\source\_posts` 文件夹，然后再推送上去就行了，原理就是当我们在执行 `hexo g` 命令时，`.md` 文件就会被转化成 HTML 文件，但是有时候我们需要新建一个独立的页面，这个页面不使用主题的渲染，具有自己独立的样式，可以放一些自己的作品，相册什么的，以下就介绍这种独立页面的实现方法。

# 方法一：

使用 Hexo 提供的跳过渲染配置，在博客根目录的配置文件 `_config.yml` 里找到 `skip_render` 关键字，在后面添加想要跳过渲染的页面，比如我们创建 `\source\about\index.html`， 配置文件填写：`skip_render: about\**`，那么就表示 `\source\about` 里所有的文件将跳过渲染，里面的文件将会被直接复制到 public 文件夹，此时就会得到一个独立的 about 页面；官方文档：[https://hexo.io/docs/configuration](https://hexo.io/docs/configuration)

# 方法2

在文章头部的 Front-matter 里添加配置 `layout: false` 来跳过渲染配置，比如我们要使 about 页面跳过渲染，创建 `\source\about\index.md`，将这个页面的相关 HTML 代码写进`.md`文件并保存，然后在 `index.md` 的头部写入：

```markdown
---
layout: false
---
{% raw %}
xxxxxxxxxxxxx
xx HTML代码 xx
xxxxxxxxxxxxx
{% endraw %}
```

PS：Front-matter 是 `.md` 文件最上方以 — 分隔的区域，用于指定个别文件的变量，官方文档：[https://hexo.io/docs/front-matter](https://hexo.io/docs/front-matter)
```markdown
.md文件中的 HTML 代码要用 {% raw %} 和 {% endraw %} 包起来，使其能正确编译里面的代码。
```