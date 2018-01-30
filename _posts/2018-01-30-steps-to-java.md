### steps to java



##### 字符串和字符数组转换


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


##### 将数组转换为字符串
```
//array工具类的toString方法，将数组转换为字符串
Arrays.toString(arr[i])
```





java中的IO流中的输出流一般都有flush这个操作，这个操作的作用是强制将缓存中的输出流（字节流，字符流等）强制输出。


##### 数组的遍历
```
ArrayList lis = new ArrayList();
ls.add("list");
for(int i=0;i<lis.size();i++)
{
  System.out.println(lis.get(i));
}
```


##### 类型转换
int类型强制转换为string

```
int a = 10;
String s = "";
s = String.valueOf(a);
```



```
public static int nbDig(int n, int d) {
	
	int res = 0;
	char c = Integer.toString(d).charAt(0);
	int sum = 0;
	//char[] c;
	String a = "";
	for(int i=0;i<=n;i++){
		sum = i*i;
		a = "" + String.valueOf(sum);
		for(int j=0;j<a.length();j++){
			if(a.charAt(j)==c){
				res++;
			}
		}
	}
	//System.out.println(a);

	
	return res;
```

```
Character.getNumericValue(numChar)
```

```
//字符数组转换为字符串
strStringType= String.valueOf(chrCharArray);
```



通过句柄和指针直接访问对象



System.arraycopy()实现数组间的复制：

```
public static void (Object src,int srcPos,Object dest,int destPos,int length)
/*
src:源数组
srcPos:源数组要复制的起始位置
dest:目标数组
destPos:目标数组的起始位置
length:复制的长度
src和dest必须为同类或可相互转换类型的数组
*/
```





1、java hbase

2、netty

3、数制转换

4、multi-thread