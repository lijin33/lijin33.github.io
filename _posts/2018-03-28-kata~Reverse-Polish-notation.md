---
layout:     post              # 使用的布局（不需要改）
title:      kata~Reverse Polish notation        # 标题 
subtitle:   每日kata              #副标题
date:       2018-03-28        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Kata
    - Java
    - Re
---

##### 逆波兰表达式

Your job is to create a calculator which evaluates expressions in [Reverse Polish notation](http://en.wikipedia.org/wiki/Reverse_Polish_notation).

For example expression `5 1 2 + 4 * + 3 -` (which is equivalent to `5 + ((1 + 2) * 4) - 3` in normal notation) should evaluate to `14`.

Note that for simplicity you may assume that there are always spaces between numbers and operations, e.g. `1 3 +` expression is valid, but `1 3+` isn't.

Empty expression should evaluate to `0`.

Valid operations are `+`, `-`, `*`, `/`.

You may assume that there won't be exceptional situations (like stack underflow or division by zero).



#####Solution

正则&栈

第一次做的时候没判断栈为空的情况，加入判断之后√

做计算处理的时候有些麻烦了，用了最笨的方法_(:з」∠)_

```
public static double evaluate(String expr) {
	    // TODO: Your awesome code here
      if(expr=="") {
    	  return 0;
      }
	  String[] w = expr.split("\\s+");
	  int l = w.length;
	  double c = 0;

	  Stack<String> st = new Stack<String>();
	 
	  for(int i=0;i<l;i++) {
		  Pattern pattern = Pattern.compile("-?[0-9]+\\.?[0-9]*");
	      Matcher isNum = pattern.matcher(w[i]);
		  if(isNum.matches()) {
			  st.push(w[i]);
		  }
		  else {
			  double a = Double.parseDouble(st.pop());
			  double b = Double.parseDouble(st.peek());
			  if(!st.empty()) {
				  st.pop();
			  }
			  if((w[i].toCharArray())[0]=='+') {
				  c = b + a;
			  }
			  if((w[i].toCharArray())[0]=='-') {
				  c = b - a;
			  }
			  if((w[i].toCharArray())[0]=='*') {
				  c = b * a;
			  }
			  if((w[i].toCharArray())[0]=='/') {
				  c = b / a;
			  }
			  st.push(String.valueOf(c));
		  }
	     
	  }			   
	  			 
	    return Double.parseDouble(st.peek());
	  }
```

