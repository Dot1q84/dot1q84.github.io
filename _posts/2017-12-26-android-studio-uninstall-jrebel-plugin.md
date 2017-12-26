---
layout:     post
title:      "Android Studio卸载JRebel插件提示`Required plugins weren't loaded`"
subtitle:   " \"阴魂不散的插件\""
date:       2017-12-26 9:01:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
    - Jrebel
---

最近试用了一个效率工具JRebel，由于某些原因需要删除该插件。

结果删除之后，重启Android Studio发现一直提示`Required plugins weren't loaded`，如图所示。

![问题提示](/img/post-imgs/2017-12-26-android-studio-uninstall-jrebel-plugin-1.png)

虽说不影响程序运行调试，但是每次打开都被警告一下总感觉不太爽。

后面查找了工程下的配置文件，发现`.idea`目录下有个`externalDependencies.xml`文件中含有JRebel plugin相关的配置，遂删除该行保存文件并重启Android Studio，一身清爽。

![externalDependencies.xml](/img/post-imgs/2017-12-26-android-studio-uninstall-jrebel-plugin-2.png)

PS：JRebel还是挺好用的→_→
 
以上。