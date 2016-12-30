---
layout:     post
title:      "Markdown使用小记_20161230"
subtitle:   " \"用着用着，从陌生到熟悉\""
date:       2016-12-30 14:20:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Markdown
---

### 1、Markdown如何插入整段代码
空四格输入。简单点说就是全选代码然后Tab。

Markdown 会把每行前面空四格的文本转换为代码块。如果是GFM的话，也支持这种语法，同时，还支持围栏式代码块即代码块包裹在 ``` 之间。 via [abirdcfly](https://segmentfault.com/u/abirdcfly)

### 2、Markdown中插入图片有什么技巧？
说用图床的，要么是图床的托，要么就是根本就没有研究明白。在repository的根目录下建立文件夹，然后将图片放在其中，在写链接的时候直接使用自己的GitHub Pages根路径加图片地址访问即可。例如：

	![xxxxxx](https://xxxxxx.github.io/项目名称/images/图片名称.png)

via [Victory Cai(知乎)](https://www.zhihu.com/question/21065229/answer/137816523)
