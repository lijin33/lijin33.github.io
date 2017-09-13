---
layout:     post              # 使用的布局（不需要改）
title:      记两个和javascript有关的问题        # 标题 
subtitle:                  #副标题
date:       2017-09-08        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - robotframework
    - javascript
---

1、跑web自动化时遇到下面这种日历框，如何选择日期值

![image.png](http://upload-images.jianshu.io/upload_images/4886646-471e7983ae7c8f54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

先移除readonly属性，然后再赋个值
```
execute javascript    window.document.getElementById('${id}').removeAttribute("readonly")
execute javascript    window.document.getElementById('${id}').value='${date}'
```

2、想要滚动屏幕，但以下这种方式不好使
```
execute javascript    document.documentElement.scrollTop=1200
```
可以指定滚动窗口的class值或id值，然后进行滚动
```
Execute Javascript    document.getElementsByClassName('${class_name}')[0].scrollTop=30000;
```
