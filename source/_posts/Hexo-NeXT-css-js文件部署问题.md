---
title: Hexo-NeXT 自定义静态页面部署问题
date: 2023-01-07 12:39:39
tags: Hexo
categories: Issues
---
问题定位
---
NexT 主题下部署自定义静态页面时（比如本站 love page），写好的 html 达不到想要的效果。

预期效果：

![](/images/20230107/预期效果.gif)

经过排查可以分为下面两个子问题：

1. 自定义 html 不能作为一个独立的静态页面展示，而是作为NexT框架下的一个组件，如普通博文一般；
![](/images/20230107/q1.gif)
2. CSS 和 JS 文件的渲染没有起效。
![](/images/20230107/q2.png)
<!--more-->

原因分析
---
1. 执行 `hexo generate` 时，NexT 会渲染 source 文件夹下的所有 html 文件，所以会将我们写好的静态页面也一同渲染到框架中。
2. 执行 `hexo generate` 时，NexT 会在 CSS 和 JS 文件开头加一行
```css
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/hint.css/2.4.1/hint.min.css">
```
正是这行代码导致了 CSS 和 JS 文件不可用。

解决方案
---
1. 在站点配置文件（即根目录下的 _config.yml）中，`skip_render` 字段后面添加不需要渲染的文件名，比如：
```
skip_render: love/*  # 不渲染 blog/source/love 路径下所有文件
```
或者在不需要渲染的文件开头添加
```html
layout: false
---
```
2. 将 CSS 和 JS 文件放在主题的 source 目录下（`blog/themes/next/source`），而非直接网站的 source 目录下（`blog/source`），并将 html 中的相关路径改为 `../`
