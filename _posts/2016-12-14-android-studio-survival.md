---
layout:     post
title:      "AndroidStudio填坑记_20161214"
subtitle:   " \"记录爬出坑的方式\""
date:       2016-12-14 17:56:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
---
最近把项目由eclipse迁移至AS的过程中发现了几个问题，记录一下处理方式。
 
## 问题一
#### 描述：
有时候打开as时，会出现卡在Gradle Build Running......就是那种好几分钟都没什么变化的，一开始以为是更新gradle什么的速度慢，后面发现开vpn也没用。
#### 解决方法：
把腾x电脑管家关闭，重启as后瞬间就处理完了。估计3x0之类的管理软件都有类似的问题。
[参考链接](http://blog.csdn.net/hwe_xc/article/details/51074845)

## 问题二
#### 描述：
build工程的时候会出现以下报错

	:mergeDebugResources
	Error: java.util.concurrent.ExecutionException: com.android.ide.common.process.ProcessException: 
	:mergeDebugResources FAILED
	
	FAILURE: Build failed with an exception.
	
	* What went wrong:
	Execution failed for task ':mergeDebugResources'.
	> Error: java.util.concurrent.ExecutionException: com.android.ide.common.process.ProcessException: 
	
	* Try:
	Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.
	
	BUILD FAILED
	

之后尝试过加上stacktrace、info、debug参数，皆没有更具体的错误信息描述。
此时工程所使用的是`com.android.tools.build:gradle:2.2.3`，AS的版本也是2.2.3。
搜了下只有github上有个小哥碰上类似的问题，暂无解决方案。
 
#### 解决方法：
摸索了很久后，将build.gradle文件中的gradle版本改为`com.android.tools.build:gradle:2.2.2`。重新build后出现错误日志说是9png文件不符合标准，重新处理俩.9.png图片后build遂成功解决。
问题原因：怀疑是AS2.2.3自带的2.2.3gradle存在错误日志输出缺陷，导致无法打印具体信息。
 
 
## 问题三
#### 描述：
一开始AS刚更新到2.2.3的时候，一编辑module下的build.gradle文件，AS就会马上卡得不要不要的，就是那种突然被定住，连光标都不闪，过几分钟会有缓解，再编辑俩字符又卡住。
但是在代码编辑就完全正常。
#### 解决方法：
参考了知乎上的回答，说是google repository 和Android support repository都得有，发现SDK中的google repository没装，安装上去重启as后顺畅得飞起
[参考链接 回答作者: 奔驰的小鸟](http://zhihu.com/question/51285782/answer/131439051)
 
 
## 问题四
#### 描述：
迁移后build成功的工程，运行后会出现两个图标相同的App，一个正常，另一个名为MainActivity且点开就闪退，删除其中一个则两个都会被删除。
#### 解决方法：
检查工程引用的library，发现某个library的AndroidManifest.xml里生成了如下代码

	<category
		android:name="android.intent.category.LAUNCHER"
	/>

由于当前工程和所引用的library工程有重复声明LAUNCHER导致重复生成，删除library中的LAUNCHER声明，重新运行就正常了。
[参考链接](http://blog.csdn.net/y505772146/article/details/47859907)
 
## 问题五
#### 描述：
迁移后的工程运行时无法找到.so文件，引发crash
 
#### 解决方法：
确保引用的.jar和.so都在libs目录里（与eclipse目录结构一致）
在build.gradle中的`android{sourceSet{main{}}}`里添加`jniLibs.srcDirs = ['libs']`，重新build运行即可。
