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

说用图床的，要么是图床的托，要么就是根本就没有研究明白。在repository的根目录下建立文件夹，然后将图片放在其中，在写链接的时候直接使用自己的GitHub Pages根路径加图片地址访问即可。比如说你的github.io项目里有个叫img的文件夹里有张图叫logo.png：

	![这里是图片备注](https://xxxxxx.github.io/img/logo.png)

via [Victory Cai(知乎)](https://www.zhihu.com/question/21065229/answer/137816523)

PS:

继续上面的例子，如果用jekyll构造的blog，可以直接上相对路径：

	![这里是图片备注](../../../../img/logo.png)

每个`..`代表往上回退一级，至于为什么回退这么多层，跟jekyll生成的目录结构有关系，在本地启动jekyll服务后看下根目录里`_site`目录里的文件结构，从某一篇文章的目录访问到图片所在的目录应该怎么切换。

### 3、github emoji速查表

[https://github.com/guodongxiaren/README/blob/master/emoji.md](https://github.com/guodongxiaren/README/blob/master/emoji.md)

推荐这个项目，列举说明了github readme很多md的样式和写法

via [guodongxiaren](https://github.com/guodongxiaren/README)