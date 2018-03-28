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



#####Solution(未验证,codewar 500)

正则&栈

```
 public static double evaluate(String expr) {
			    // TODO: Your awesome code here
			  String[] w = expr.split("\\s+");
			  int l = w.length;
			  double c = 0;
			  if(l==0) {
				  return 0;
			  }
			 
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
					  //System.out.println(a);
					  //System.out.println(b);
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
					  //System.out.println(st.peek());
				  }
			     
			  }			   
			  			 
			    return Double.parseDouble(st.peek());
			  }
```

