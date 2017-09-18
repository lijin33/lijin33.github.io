---
layout:     post              # 使用的布局（不需要改）
title:      KeyError:'desired'问题解决        # 标题 
subtitle:   定时更新library很重要哇=。=                  #副标题
date:       2017-09-18        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Android
    - Appium
    - Automation
    - Trouble-shoot
---


### Appium KeyError: 'desired'

之前在Appium1.6.5-新坑旧坑一起坑时说起了这个问题，刚才又仔细看了一下，发现其实是AppiumLibrary版本的问题  

虚拟机上的AppiumLibrary版本是1.4.1，_applicationmanagement.py中：  


![key-error.jpg](http://upload-images.jianshu.io/upload_images/4886646-6a9e55b7fab7e6de.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


因为传入的参数并没有'desired'这一项，所以就报错啦  

我本机1.4.5版本的AppiumLibrary-applicationmanagement.py：  


![key-error1.jpg](http://upload-images.jianshu.io/upload_images/4886646-31f40dcb0b6a8bd6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


升级AppiumLibrary版本或删掉'desired'之后，case均可以正常运行了，还是推荐升级library版本嗯:sweat_smile:

