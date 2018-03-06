---
layout:     post              # 使用的布局（不需要改）
title:      kata~Reversed String        # 标题 
subtitle:   每日kata              #副标题
date:       2018-03-06        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Kata
    - Java
---



每日kata之Reversed String:

```
Kata.solution("world") //return "dlrow"
```



solution：

```
public static String solution(String str) {
    // Your code here...
	String res;
	char[] c = str.toCharArray();
	char d;
	int l = c.length;
	for(int i=0;i<l/2;i++) {
		d = c[i];
		c[i] = c[l-i-1];
		c[l-i-1] = d;

	}
	res = String.valueOf(c);
	
	
	
    return res;
  }
```

