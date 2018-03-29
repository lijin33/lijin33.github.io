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



##### Detail:

You are given a list/array which contains only integers (positive and negative). Your job is to sum only the numbers that are the same and consecutive. The result should be one list.

Extra credit if you solve it in one line. You can asume there is never an empty list/array and there will always be an integer.

Same meaning: 1 == 1

1 != -1

\# Examples:

```
[1,4,4,4,0,4,3,3,1] # should return [1,12,0,4,6,1]

"""So as you can see sum of consecutives 1 is 1 
sum of 3 consecutives 4 is 12 
sum of 0... and sum of 2 
consecutives 3 is 6 ..."""

[1,1,7,7,3] # should return [2,14,3]
[-5,-5,7,7,12,0] # should return [-10,14,12,0]
```



##### Solution:

明明没看答案就做对了却没给我积分QAQ...

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

