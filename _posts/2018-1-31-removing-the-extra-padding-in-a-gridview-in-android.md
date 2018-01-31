---
layout:     post
title:      "删掉GridView默认的内边距问题"
subtitle:   " \"还有这种操作？\""
date:       2018-1-31 15:44:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
---

偶然发现之前写的一个GridView带了个灰色边框，对照了效果图发现是多余的，遂查代码发现并没有加padding之类的设定。

//  - -|| 难不成是自带的

后面发现布局里GridView加上`android:listSelector="@null"`属性就可以去掉该边框了。StackOverFlow也有类似的问答。参考链接 [http://stackoverflow.com/questions/6876861/removing-the-extra-padding-in-a-gridview-in-android](http://stackoverflow.com/questions/6876861/removing-the-extra-padding-in-a-gridview-in-android)

涨姿势了。