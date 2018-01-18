```
public static String accum(String s) {
    // your code
    StringBuilder res = new StringBuilder();
    int i=0;
    //toCharArray():将字符串转换为字符数组
    for(char c:s.toCharArray()){
        if(i>0)
            res.append('-');
        
        res.append(Character.toUpperCase(c));
        for(int j=0;j<i;j++)
        {  
        	//Character是Char的包装类，Char是基本类型。
            res.append(Character.toLowerCase(c));
        }
     i++;
     
    }
    
    //将res强制转换为String类型
    return res.toString();
}
```





```
//array工具类的toString方法，将数组转换为字符串
Arrays.toString(arr[i])
```





java中的IO流中的输出流一般都有flush这个操作，这个操作的作用是强制将缓存中的输出流（字节流，字符流等）强制输出。



```
ArrayList lis = new ArrayList();
ls.add("list");
for(int i=0;i<lis.size();i++)
{
  System.out.println(lis.get(i));
}
```

