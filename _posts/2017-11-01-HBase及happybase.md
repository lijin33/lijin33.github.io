---
layout:     post              # 使用的布局（不需要改）
title:      HBase及happybase        # 标题 
subtitle:   记录               #副标题
date:       2017-11-01        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Python
    - HBase
    
---

好久不写，差点忘了这个博客(￣_,￣ )
最近在搞一个物联网项目，用到了HBase，由于hbase shell实在是有点麻烦，所以查了一下关于happybase的资料
#### 安装thrift
`pip install thrift`
安装时报错，未找到xx module下的xx方法blablabla
解决方法：删除Python/Lib下的fcntl.py及fcntl.pyc
从以下链接重新下载fcntl.py并放入Lib文件夹，重新安装thrift，ok (*https://pypi.python.org/pypi/micropython-fcntlk*)
#### 安装happybase
`pip install happybase`
