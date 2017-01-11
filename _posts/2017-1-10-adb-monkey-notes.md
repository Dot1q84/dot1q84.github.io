---
layout:     post
title:      "adb monkey学习笔记_20170110"
date:       2017-01-10 17:00:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
    - notes
---
## 基本环境

- android开发环境
- python

## Monkey命令

- 简单用法帮助

	`adb shell monkey --help`

- 使用monkey随机做1000个操作

	`adb shell monkey 1000`

- 指定某程序随机做1000个操作（如计算器）

	`adb shell monkey -p com.android.calculator2 1000`

	##### `-p`参数可以使用多个，每个`-p`参数只能用于一个包。
- 打印具体的操作过程日志为`-v`参数

	`adb shell monkey -v -p com.android.calculator2 1000`

- 指定启动类型参数`-c <main-category>`
  如果用此参数指定了一个或几个类别，Monkey将只允许系统启动被这些类别中的某个类别列出的Activity。如果不指定任何类别，Monkey将选 择下列类别中列出的Activity： `Intent.CATEGORY_LAUNCHER`或`Intent.CATEGORY_MONKEY`。要指定多个类别，需要使用多个`-c`参数，每个`-c`参数只能用于一个类别。
- 指定某程序每10毫秒间隔做一次随机操作，共1000次

	`adb shell monkey -p com.android.calculator2 --throttle 1000 10`

	##### --throttle参数可用于指定随机间隔时间

- 指定一个seed，用于复现相同的随机操作。如seed为100，执行50次。如果下次执行此命令指定的seed和随机次数相同，可以复现相同的操作和结果。

	`adb shell monkey -v -p com.android.calculator2 -s 100 50`

- 设定触摸事件百分比，即点击操作所占所有随机操作的比例，在不指定为100%的情况下会出现包括touch、motion、trackball等等在内的各种操作。

	`adb shell monkey --pct-touch <percent>`

	如100%执行触摸事件1000次

	`adb shell monkey -v -p com.android.calculator2 --pct-touch 100 1000`

- 其他事件，设定某事件的百分比，剩下未指定的百分比部分则其它事件随机操作。

	`adb shell monkey <action> <percent>`

	除了touch之外还有很多种操作事件类型可以指定。列举一下：
		0. 触摸点击事件 【--pct-touch】
		   注：触摸事件是一个down-up事件，它发生在屏幕上的某单一位置
		1. 动作事件 【--pct-motion】
		   注：动作事件由屏幕上某处的一个down事件、一系列的伪随机事件和一个up事件组成
		2. 轨迹球事件 【--pct-trackball】
		   注：轨迹事件由一个或几个随机的移动组成，有时还伴随有点击
		3. 基本导航事件 【--pct-nav】
		   注：导航事件由来自方向输入设备的up/down/left/right组成
		4. 主要导航事件 【--pct-majornav】
		   注：这些导航事件通常引发图形界面中的动作，如：5-way键盘的中间按键、回退按键、菜单按键
		5. 系统导航事件 【--pct-syskeys】
		   注：这些按键通常被保留，由系统使用，如Home、Back、Start Call、End Call及音量控制键
		6. 启动Activity事件（切换Activity） 【--pct-appswitch】
		   注：在随机间隔里，Monkey将执行一个startActivity()调用，作为最大程度覆盖包中全部Activity的一种方法。
		7. 其它不常用类型事件 【--pct-anyevent】
		   注：它包罗了所有其它类型的事件，如：按键、其它不常用的设备按钮、等等。

	组合使用时，比如touch事件50%，motion事件10%，appswitch事件40%，共做100次操作。此时所有percent都有指定的操作，故无随机操作。
	
	`adb shell monkey -v -p com.android.calculator2 --pct-touch 50 -pct-motion 10 --pct-appswitch 40 100`
	
	再如touch事件40%，motion事件10%，其他的50%不指定，则这50%的操作将会除touch和motion外的其他所有事件种类随机操作。
	
	`adb shell monkey -v -p com.android.calculator2 --pct-touch 40 -pct-motion 10`
	
	### log输出中的event percentage，在不同版本的SDK下显示顺序可能会不一样，需要结合源码枚举量确定具体映射顺序，[参考资料](http://blog.csdn.net/jlminghui/article/details/42268187)	


- 设定monkey忽略崩溃和异常继续运行

	`adb shell monkey --ingnore-crashes <event-count>`

- 设定monkey忽略超时事件（ANR）继续运行

	`adb shell monkey --ignore-timeouts <event-count>`

	ANR输出日志可以到`/data/anr/traces.txt`文件查看
	`adb shell`
	`more /data/anr/traces.txt`

- 设定monkey忽略权限许可错误

	`adb shell monkey --ignore-security-exceptions`


## MonkeyScript

### 1 命令简介

- 执行Monkey脚本的命令：

	`adb shell monkey -f <scriptfile> <event-count>`

#### 1.1 DispatchTrackball命令

轨迹球事件

	/**
	 * downtime 键最初按下的时间
	 * eventide 事件发生时间
	 * action 具体操作（0:按下, 1:弹起）
	 * x 坐标点x轴值
	 * y 坐标点y轴值
	 * pressure 压力的大小，范围[0,1]
	 * size 触摸的近似值，范围[0,1]
	 * metastate 当前按下meta键的标识
	 * xprecision x坐标精确值
	 * yprecision y坐标精确值
	 * device 事件来源，范围[0,x]，0表示不来自物理设备
	 * edgeflags 超出屏幕范围
	 **/	
	DispatchTrackball(long downtime, long eventide, int action, float x, float y, float pressure, float size, int metastate, float xprecision, float yprecision, int device, int edgeflags)

比较重要的参数有action、x和y，其他参数基本上不经常调整。

#### 1.2 DispatchPointer命令

点击事件

	/**
	 * downtime 键最初按下的时间
	 * eventide 事件发生时间
	 * action 具体操作（0:按下, 1:弹起）
	 * x 坐标点x轴值
	 * y 坐标点y轴值
	 * pressure 压力的大小，范围[0,1]
	 * size 触摸的近似值，范围[0,1]
	 * metastate 当前按下meta键的标识
	 * xprecision x坐标精确值
	 * yprecision y坐标精确值
	 * device 事件来源，范围[0,x]，0表示不来自物理设备
	 * edgeflags 超出屏幕范围
	 **/	
	DispatchPointer(long downtime, long eventide, int action, float x, float y, float pressure, float size, int metastate, float xprecision, float yprecision, int device, int edgeflags)

比较重要的参数有action、x和y，其他参数基本上不经常调整。

#### 1.3 DispatchString命令

输入字符串事件

	/**
	 * text 要输入的字符串
	 **/
	DispatchString(String text)

#### 1.4 LaunchActivity命令

启动应用

	/**
	 * package 包名
	 * Activity Activity名
	 **/
	LaunchActivity(String packageName, String activityName)
### 注意：如果某个Activity在AndroidManifest中没有声明`android:exported="true"`，是无法从外部调用的，会导致LaunchActivity命令失效

#### 1.5 UserWait命令

等待事件

	/**
	 * millisecond 等待时间，单位毫秒
	 **/
	UserWait(int millisecond)

#### 1.6 DispatchPress命令

按下键值

	/**
	 * keycode 要按下的键值，如回车键的keycode为66
	 **/
	DispatchPress(int keycode)

### 2 示例

#### 注：使用Android sdk里的tools目录中提供的工具UI Automator Viewer可以拾取查看APP界面控件的id和坐标范围等一系列信息，这货的文件名一般为uiautomatorviewer。

准备脚本文件mock.script
内容如下

	typ=user
	count=10
	speed=1.0
	start data >>

	LaunchActivity(com.example, com.example.MainActivity) #启动包名com.example程序并打开com.example.MainActivity
	UserWait(2000) #等待2秒
	DispatchPointer(10,10,0,100,100,1,1,-1,1,1,0,0) #按下坐标为（100,100）的点
	DispatchPointer(10,10,1,100,100,1,1,-1,1,1,0,0) #结束点击，弹起
	DispatchString(test) #执行输入字符串test
	UserWait(1000) #等待1秒
	DispatchPress(66) #点击回车按钮

具体点击位置坐标值可以使用工具查看具体APP界面确定坐标取值。
#### 如果编写脚本是在电脑上编写，需要把script文件push到手机上再通过adb shell执行。
如：
	`adb push mook.script /data/local/tmp/`
	`adb shell monkey -f /data/local/tmp/mook.script 2` ##执行两次脚本

MonkeyScript无法进行调试，执行过程出现错误也不会报错，需要对照脚本源码和运行效果判断出错点。

## MonkeyRunner(Python 脚本）

### 0 执行MonkeyRunner脚本

比如执行一个叫demo.py的MonkeyRunner脚本

	monkeyrunner demo.py

monkeyrunner工具在Android SDK的tools目录中提供，配好了环境变量一般在哪都能执行。

### 1 API简介

#### 1.1 MonkeyRunner - alert 警告框

	void alert(String message, String title, String okTitle)

例如demo1_1.py:
	#!/usr/bin/python
	#-*- UTF-8 -*-
	from com.android.monkeyrunner import MonkeyRunner
	MonkeyRunner.alert('弹框消息体', '弹框标题', '按钮名称')


#### 1.2 MonkeyRunner - waitForConnection 等待设备连接

用这个命令可以通过adb连接上一台设备。
timeout参数为超时时间，单位是秒。
当有多个deviceId的时候，需要指明具体哪个设备。
执行该命令会返回一个MonkeyDevice对象，可以调用MonkeyDevice相关的API对设备进行操作。

	MonkeyDevice waitForConnection(float timeout, String deviceId)

获取目前adb连接的device的id可以通过执行以下指令：

	adb devices

假设执行后显示log为

	List of Devices attached
	192.168.31.100:5555 device

那么连接这台设备，设定5s超时的命令就是

	device = MonkeyRunner.waitForConnection(5, "192.168.31.100:5555")
#### 1.3 MonkeyDevice - drag 拖动

start和end分别是拖动的起止点位置。
duration为拖动持续时间。
steps表示通过几步来实现，默认10。

	drag(tuple start, tuple end, float duration, integer steps)

#### 1.4 MonkeyDevice - press 按键

keycode即为按键的code。
type表示操作种类，取值一般有DOWN、UP、DOWN_AND_UP。

	press(string keycode, dictionary type)

比如指定某个按键按下又弹起

	press(code, DOWN)
	press(code, UP)

或者

	press(code, DOWN_AND_UP)

两者等价，前者配合延时命令可以适用于某些hold住按键数秒后松开的场景

#### 1.5 MonkeyDevice - startActivity 启动应用

	startActivity(string package+'/'+activity)

#### 1.6 MonkeyDevice - touch 点击

x和y对应点击位置的x和y坐标值。
type为操作种类，取值一般有DOWN、UP、DOWN_AND_UP。

	touch(integer x, integer y, integer type)

#### 1.7 MonkeyDevice - type 输入

x和y对应点击位置的x和y坐标值。
type为操作种类，取值一般有DOWN、UP、DOWN_AND_UP。

	type(string message)

#### 1.8 MonkeyDevice - takeSnapshot 截屏（较为重要的方法！！！）

调用takeSnapshot方法会返回一个MonkeyImage类型的图片，可以使用MonkeyImage相关API进一步处理。

	MonkeyImage takeSnapshot()

#### 1.9 MonkeyImage - sameAs 图像对比

otherImg为需要与当前图片对比的其他图片。
percent为相似度百分比。
执行后会判断两张图片的相似度是否达到指定百分比后返回true或false。

	boolean sameAs(MonkeyImage otherImg, float percent)

#### 1.10 MonkeyImage - writeToFile 保存图像文件

path为指定的保存路径。
format为保存的文件类型，如jpeg、png等等。

	boolean sameAs(MonkeyImage otherImg, float percent)

### 2 综合示例

demo.py内容如下：

	#!/usr/bin/python
	#-*- UTF-8 -*-
	from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice, MonkeyImage
	#连接设备
	device = MonkeyRunner.waitForConnection(3, "192.168.31.100:5555")
	#启动APP
	device.startActivity("com.example/com.example.MainActivity")
	MonkeyRunner.sleep(2)
	#点击坐标为（100, 100）的点
	device.touch(100, 100, "DOWN_AND_UP")
	MonkeyRunner.sleep(1)
	#输入字符串test
	device.type('test')
	MonkeyRunner.sleep(1)
	#点击回车键
	device.press('KEYCODE_ENTER', 'DOWN_AND_UP')
	MonkeyRunner.sleep(1)
	#截图并保存test.png在脚本所在目录
	image = device.takeSnapshot()
	image.writeToFile('./test.png', 'png')

接下来执行这个脚本

	monkeyrunner demo.py

### 3 补充说明

MonkeyRunner等只能实现功能性的操作，如果要设定执行次数之类的要求可以通过python脚本配合实现。