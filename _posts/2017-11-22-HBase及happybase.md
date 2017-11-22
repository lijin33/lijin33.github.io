---
layout:     post              # 使用的布局（不需要改）
title:      使用happybase happy地查询hbase数据        # 标题 
subtitle:   记录               #副标题
date:       2017-11-22        # 时间
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

#### 用happybase进行hbase中数据的增删改查

前提：已经安装happybase库(pip install happybase)，已有hbase环境并开启thrift通讯端口(nohup hbase thrift start &)，thrift默认端口为9090，10.10.30.200为hbase主机ip

##### 查询

scan方法：

```
scan(self, row_start=None, row_stop=None, row_prefix=None,
         columns=None, filter=None, timestamp=None,
         include_timestamp=False, batch_size=1000, scan_batching=None,
         limit=None, sorted_columns=False, reverse=False)
```

参数：

row_start、row_stop：起始和终止rowkey，查询两rowkey间的数据

row_prefix：rowkey前缀。注：使用row_prefix的时候，row_start和row_stop不能使用

filter：要使用的过滤器(hbase 0.92版本及以上生效)

timestamp：按指定时间戳查询

reverse：默认为False。为True时，scan结果按rowkey倒序排列

e.g：

```
import happybase

connection = happybase.Connection('10.10.30.200',9090)
#获取table实例
t = connection.table('iot_flow_history')
#按row前缀遍历查询数据，因该工程存入hbase的rowkey为id-timestamp，使用rowkey查询十分不方便，故使用row_prefix进行查询
for key,value in t.scan(row_prefix='1100HD72004016A16'):
    print key,value
```



##### 增加&编辑

put方法：

```
put(self, row, data, timestamp=None, wal=True)
```

e.g：

```
import happybase

connection = happybase.Connection('10.10.30.200',9090)
#获取table实例
t = connection.table('iot_flow_history')

#该表已有column familylz，列id和realValue
student = {"lz:id":'test',"lz:realValue":"18"}
t.put(row="test",data = student)
```

△ 如put中的rowkey已存在，则为修改数据



##### 删除

delete方法：

```
delete(self, row, columns=None, timestamp=None, wal=True)
```

row：删除rowkey为row的数据

columns：指定columns参数时，删除

e.g：

```
import happybase

connection = happybase.Connection('10.10.30.200',9090)
#.delete('test')
t = connection.table('iot_flow_history')
#删除rowkey为test的数据
t.delete('test')
```

删除rowkey为student2的name数据：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/171122/b3hleADl1b.png?imageslim)

```
t = connection.table('students')
t.delete(row='student2',columns=["account:name"])
```

删除成功：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/171122/laHFG93G55.png?imageslim)



##### 批量处理

batch方法：

```
batch(self, timestamp=None, batch_size=None, transaction=False,
          wal=True)
```

1、批量操作

```
import happybase

connection = happybase.Connection('10.10.30.200',9090)
#.delete('test')
t = connection.table('iot_flow_history')

student111 = {"account:name":"xiaohuang","address:zipcode":"222222"}
student222 = {"account:name":"xiaolv","address:zipcode":"333333"}
t = connection.table('students')
bat = t.batch()

#添加student111和student222，也可以进行批量删除
bat.put('student111',data = student111)
bat.put('student222',data = student222)
#bat.delete('student111')
bat.send()
```

![mark](http://owl3le8ji.bkt.clouddn.com/blog/171122/JiC9ECfiF7.png?imageslim)



2、使用with管理批量

```
import happybase

connection = happybase.Connection('10.10.30.200',9090)
#.delete('test')
t = connection.table('iot_flow_history')

student333 = {"account:name":"xiaolan","address:zipcode":"323232"}
t = connection.table('students')

#添加student333并删除student222
with t.batch() as bat:
    bat.put('student333',data = student333)
    bat.delete('student222')
```



##### 检索

row方法及rows()方法

检索一条：

```
r = t.row('student444')
```

检索多条：

```
r = t.rows(['student444','student333'])
```

返回结果：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/171122/m96heHaA1L.png?imageslim)



##### 查询指定cell的数据

```
cells(self, row, column, versions=None, timestamp=None,
          include_timestamp=False)
```

e.g：

```
import happybase

connection = happybase.Connection('10.10.30.200',9090)
#.delete('test')
t = connection.table('iot_flow_history')

#查询rowkey为student444，column为account:name的cell的数据，包含时间戳
print t.cells('student444','account:name',include_timestamp=True)
```

结果：

```
[('xiaolan', 1511343966713L)]
```



暂时就这些0v0