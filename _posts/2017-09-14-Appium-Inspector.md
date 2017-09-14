---
layout:     post              # 使用的布局（不需要改）
title:      Appium1.6.5-Inspector on Window!       # 标题 
subtitle:   Goodbye,uiautomatorviewer.bat!               #副标题
date:       2017-09-14        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Android
    - Appium
    - Automation
---

Appium1.6.5版本用了快一个月了，才找到inspector在哪里… 
再见了，uiautomatorviewer.bat，我至今都没完整的记住你的名字 嘻嘻嘻
打开appium，点击右上角放大镜


![appium-inspector1.jpg](http://upload-images.jianshu.io/upload_images/4886646-ff0ce1f971b18a59.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


设置desired capabilities，设置好之后点击save as可以保存设置，下次再用相同配置的时候就不用手动输入啦~


![appium-inspector2.jpg](http://upload-images.jianshu.io/upload_images/4886646-73b3178494371833.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


![appium-inspector-saved.jpg](http://upload-images.jianshu.io/upload_images/4886646-57e90d6edcb5e6da.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



点击Start Session，开启Inspector!

![appium-inspector-done.jpg](http://upload-images.jianshu.io/upload_images/4886646-2d7f72791bc915c8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


新版本的Inspector可以直接触发tap等操作，再也不用在机器上操作后刷新页面啦!!ヾ(o◕∀◕)ﾉヾ  


![appium-inspector-operation.jpg](http://upload-images.jianshu.io/upload_images/4886646-7fee05be59e778a6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


上图从左到右：
选择element、按坐标滑动、按坐标点击
返回、刷新、开始录制、查找element、关闭session和Inspector


![appium-inspector-searchelement.jpg](http://upload-images.jianshu.io/upload_images/4886646-92ea3d0cf431780d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


着重说一下这个查找element，类似chrome的xpath helper，love it！

录制等功能还需继续探索啦o( =·ω·= )m