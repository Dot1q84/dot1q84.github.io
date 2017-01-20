---
layout:     post
title:      "在LinearLayout中计算正确的Weight"
subtitle:   "写写增强记忆"
date:       2017-01-20 14:20:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
---

## 0. 前言

首先，我们看一下SDK里关于`weight`的说明。

<blockquote>Indicates how much of the extra space in the LinearLayout will be allocated to the view associated with these LayoutParams. Specify 0 if the view should not be stretched. Otherwise the extra pixels will be pro-rated among all views whose weight is greater than 0.</blockquote>

这里面比较重要的两个概念是`extra space`（额外空间）和`pro-rated among all views`（该控件占全部控件的比值）

## 1. 公式

### 1.1 公式一：

#### 额外空间 = 手机的宽度(或高度) - 所有控件的宽度(或高度)

### 1.2 公式二：

#### 控件的宽度(或高度) = 控件的width(或height)值 + (该控件的weight值 / 所有控件的weight的和) × 额外空间


##### 注：计算时用高度或宽度取决于LinearLayout的`orientation`是`vertical`还是`horizontal`，前者用height，后者用width。

## 2. 验证一下公式

假设手机屏幕宽度为X，线性布局里有三个横向排列的控件，他们之间的weight比值为1：2：2，根据控件的width属性不同组合，一般会有四种情况：

1. 每个控件的宽度属性都为android:layout_width="0dp"。

	额外空间为`X-0-0-0=X`（width为0表示宽度0，fill_parent或match_parent为整个屏幕宽度X）

	控件1宽度为`0+(1/(1+2+2))*X=X/5` 

	控件2宽度为`0+(2/(1+2+2))*X=2X/5`

	控件3宽度为`0+(2/(1+2+2))*X=2X/5` 

2. 每个控件的宽度属性都为android:layout_width="match_parent"

	额外空间为`X-X-X-X=-2X`

	控件1宽度为`X+(1/(1+2+2))*(-2X)=3X/5`

	控件2宽度为`X+(2/(1+2+2))*(-2X)=X/5`

	控件3宽度为`X+(2/(1+2+2))*(-2X)=X/5`

3. 其中第一个控件宽度属性为android:layout_width="match_parent"，其他两个控件宽度为android:layout_width="0dp" 

	额外空间为`X-X-0-0=0` 

	控件1宽度为`X+(1/(1+2+2))*0=X` 

	控件2宽度为`0+(2/(1+2+2))*0=0` 

	控件3宽度为`0+(2/(1+2+2))*0=0` 

4. 第一个和第二个控件宽度属性为android:layout_width="match_parent"，第三个控件宽度为android:layout_width="0dp"

	额外空间为`X-X-X-0=-X`

	控件1宽度为`X+(1/(1+2+2))*(-X)=4X/5`

	控件2宽度为`X+(2/(1+2+2))*(-X)=3X/5`

	控件3宽度为`0+(2/(1+2+2))*(-X)=-2X/5`

	这时候实际显示会是屏幕宽度4/5的控件1和屏幕宽度1/5的控件2，而控件2剩下的2X/5和控件3的负值宽度抵消（显示不下）。

## 3. 举个栗子吧

这个是我们打算实现的效果：

![此处应该有张图片](../../../../img/post-imgs/2017-1-20-weight-example.png)

效果图大概可以分成一个LinearLayout套着左半边和右半边两个LinearLayout，两边相对比例1:1。

左侧的纵向比例是4：2：3：3，右侧纵向比例是3：3：4：2。

然后我们采取的是height为`fill_parent`的方案，现在要求出左侧四个控件的weight值，设它们分别为a, b, c, d。

根据公式一，额外空间就是`X-4X=-3X`。

所以，根据公式二得出左侧四个控件宽度依次为

	X+(a/(a+b+c+d))*(-3X)=4/12*X
	X+(b/(a+b+c+d))*(-3X)=2/12*X
	X+(c/(a+b+c+d))*(-3X)=3/12*X
	X+(d/(a+b+c+d))*(-3X)=3/12*X

已知c和d相等，消元d和X后解方程。

	a/(a+b+2c)=2/9
	b/(a+b+2c)=5/18
	c/(a+b+2c)=9/36

所以得出a=8，b=10，c=9，d=9

这四个就是左侧四个控件的权重了...他们权重之和weight_sum是36。

右侧的四个同理。

PS: 示例图中色块之间的空距自行加margin或padding。