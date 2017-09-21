---
layout:     post              # 使用的布局（不需要改）
title:      Python SocketServer        # 标题 
subtitle:   我是真的很想买mbp呀TAT               #副标题
date:       2017-09-20        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Python
    - Socket 
---

#### 唠叨

前一阵子在看<Python核心编程>这本书，说起来也是断断续续的在看，今天看到这本书在socket这张已经有了折痕，实在是不忍心，于是往下看了看嗯……  

于是写一篇读书笔记，今年计划把这本和<计算机网络-自顶向下方法>看完，再看看计算机网络那本书…已经在运输层卡了好久好久了……然后手里还有一本<第一行代码—Android>……啊，欠了太多债QAQ  



#### Socket是啥

在C/S架构中作为“通信端点”的东西，书中把它比作“电话插孔”，有点生动形象。  

套接字最初是为了让同一主机上的程序间相互通信而创建的，即进程间通信(IPC)。  

有两种类型的套接字。

1、基于文件的，拥有一个“家族名字“：AF_UNIX，代表地址家族Address Family，当两个进程运行在同一台主机上时，这些套接字都是基于文件的，因为对于同一台主机上的进程来说，文件系统相当于一个共享常量。

2、基于网络的，拥有“家族名字”：AF_INET(IPV6: AF_INET6)，在所有的地址家族中，AF_INET是运用最广泛的，于是，当前我关注的以及做实验所涉及的，使用的都是AF_INET  



#### 不同风格的套接字  

面向连接的套接字 vs 无连接的套接字  

TCP vs UDP  



#### Python Socket 编程


##### socket()模块函数  

socket(socket_family, socket_type, protocol=0) 

*socket_family* : AF_UNIX或AF_INET  

*socket_type* : SOCK_STREAM(TCP)或SOCK_DGRAM(UDP)  



##### 套接字对象方法  

前提：s = socket(AF_INET,SOCK_STREAM)

###### 服务端

*s.bind()* : 将地址和端口绑定到socket上   

<e.g>

```python
ADDR = (HOST, PORT)

s = socket(AF_INET, SOCK_STREAM)

s.bind(ADDR)
```

*s.listen()* : 开始监听  

*s.accept()* : 被动接受客户端连接，一直等待知道连接到达（没用明白）


##### # 客户端

*s.connect()* : 主动发起连接

*s.connect()* : 主动发起连接，如有问题则返回错误码 


##### # 普通的套接字方法(仅列举当前用到的)  

*s.recv()* : 接受TCP消息  

*s.recv_into()* : 接受TCP消息到指定缓冲区  

*s.send()* : 发送TCP消息

*s.sendall()* : 完整发送TCP消息  

*s.getsocketname()* : 当前套接字的地址  

*s.shutdown()*: 关闭套接字

*s.close()*: 关闭当前连接  



#### SocketServer

##### # Server.py

```
#-*-coding:UTF-8-*-

from SocketServer import (TCPServer as TCP,
	 StreamRequestHandler as SRH)
from time import ctime

HOST = ''
PORT = 21567
ADDR = (HOST,PORT)

class MyRequestHandler(SRH):
	def handle(self):
		'''
		接受到一个来自客户端的消息时，会调用handle（）方法.
		StreamRequestHandler类将输入和输出套接字看作类似文件的对象，因此用readline()来获取客户端消息
		利用write()将字符串发回给客户端
		'''

		print 'connectec from :', self.client_address
		self.wfile.write('[%s] %s' % (ctime(), self.rfile.readline()))

#创造TCP服务器，无限循环等待客户端

tcpServ = TCP(ADDR,MyRequestHandler)
print 'waiting for connection...'
tcpServ.serve_forever()
```



##### # Client.py

```
#-*-coding:UTF-8-*-

from socket import *
HOST = 'localhost'
PORT = 21567
BUFSIZ = 1024
ADDR = (HOST, PORT)

while True:
	#因tcp服务器处理请求的流程是 接受连接—获取请求—关闭连接
	#每次发送消息时都需要新创建一个连接
	s = socket(AF_INET,SOCK_STREAM)
	s.connect(ADDR)
	data = raw_input('> ')
	

	if not data:
		break
	
	#因为SHR对待套接字通信的方式类似于文件，因此末尾需要加上回车和换行符
	s.send('%s\r\n' % data)
	reply = s.recv(1024)
	if not reply:
		print 'failed'
		break

	print reply.strip()
	s.close()
```


##### # 运行结果    

客户端，打印服务端返回的时间戳

![mark](http://owl3le8ji.bkt.clouddn.com/blog/170920/iFBJ6HA47E.jpg?imageslim)  



服务端，打印客户端连接的端口  

客户端连接了两次，因为客户端中的while循环为死循环，所以第一次连接结束之后又连接了一次

![mark](http://owl3le8ji.bkt.clouddn.com/blog/170920/cHL6ji1dEE.jpg?imageslim)    



#### 结束

太困了，细节和剩下的以后再说吧，先坑着…  

每次发出去之后再看都会发现很多typo…markdown大法还是练的不够啊！





