---
layout:     post
title:      "Github添加密钥时使用非默认id_rsa导致ssh验证失败"
subtitle:   " \"多个私钥引发的问题\""
date:       2016-12-29 16:50:00 +8:00
author:     "dot1q84"
header-img: "img/post-bg-2015.jpg"
tags:
    - SSH
    - GitHub
---


问题如题，起因是电脑上有多个密钥，所以都改了名字，但在做某些操作的时候仍会默认以`~/.ssh/id_rsa`文件作为校验凭据。
处理方法为在git bash执行命令时加上`-i`参数，后面带上你的私钥路径和名字，如：

`ssh -T git@github.com -i ~/.ssh/dot1q84_idrsa`
 
如果想看详细的过程日志，可以把`-T`改为`-vT`
 
以上