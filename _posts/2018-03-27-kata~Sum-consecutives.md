---
layout:     post              # 使用的布局（不需要改）
title:      kata~Sum consecutives        # 标题 
subtitle:   ArrayList              #副标题
date:       2018-03-27        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Kata
    - Java
---

#### Sum consecutives

Detail:

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180327/jelfF5Ji5e.png?imageslim)



Solution:

明明没看答案就做对了却没给我积分QAQ...注册了leetcode账号，下回试试leetcode的题...

```
public static List<Integer> sumConsecutives(List<Integer> s) {
    	List<Integer> res = new ArrayList();
    	for(int i=0;i<s.size();i++) {
    		int value = s.get(i);
    		int j = i;
    		int k = 0;
    		int t = value;
    		while((j<(s.size()-2))&&(s.get(j+1)==value)) {
    			t += value;
    			j++;
    		}
    		res.add(t);
    		k++;
    		i = j;
    	}
    	return res;
    }

```

