---
layout:     post              # 使用的布局（不需要改）
title:      kata~Convert string to camel case        # 标题 
subtitle:   每日kata              #副标题
date:       2018-03-26        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Kata
    - Java
---

Complete the method/function so that it converts dash/underscore delimited words into camel casing. The first word within the output should be capitalized only if the original word was capitalized.

e.g:

// returns "theStealthWarrior"
toCamelCase("the-stealth-warrior") 

// returns "TheStealthWarrior"
toCamelCase("The_Stealth_Warrior")



最优解应该是用的正则，稍后再研究一下

		public static String toCamelCase(String s){
			char[] c = s.toCharArray();
			String res = "";
			char flag = '-';
			if(s.length() == 0)
				return "";
			res += c[0];
			
			for(int i=1;i<c.length;i++) {
				if((c[i]=='-')||(c[i])=='_') {
					res += Character.toUpperCase(c[i+1]);
					i += 1;
					}
					else
						res += c[i];
					
				}		
		    return res;
			}