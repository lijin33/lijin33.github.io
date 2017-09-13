tags: [Python,] 

6月份的时候在组里讲了HTTP协议，之后准备要写模拟器，但是迟迟没有动手，就这么拖了两个月_(:з」∠)_ 

不能再拖啦，于是找了找资料，今天完成了基础的部分：server和client连接上了♪(^∇^*)，更细致的需求这周慢慢搞，和appium穿插着♂搞

创建socket：
```
#Socket client example in python 
import socket   #for sockets
#create an AF_INET, STREAM socket (TCP)
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print 'Socket Created'
```
socket.socket(Address family, Type): 用于创建一个socket，返回值为socket的描述符  

Address family: AF_INET（用户Internet进程间通信），AF_UNIX（用于同一台机器上的进程间通信）
Type: 套接字类型，SOCKET_STREAM主要用于TCP协议，SOCKET_DGRAM主要用于UDP协议  

如创建socket失败，会抛出socket.error异常，可用except进行捕获

socket.gethostbyname()，可以根据名字获取远程主机的IP：

    1 #!/usr/bin/python
    2 import socket
    3 import sys
    4 host = 'www.baidu.com'
    5 port = 80
    6 #s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    7 try:
    8     remote_ip = socket.gethostbyname(host)
    9 except socket.gaierror:
    10     sys.exit()
    11 print 'Socket Connected to ' + host + ' on ip ' + remote_ip

结果：
```
Socket Connected to www.baidu.com on ip 61.135.169.125
```

#####Server.py
```
#-*- coding:utf-8 -*-
  2 #!/usr/bin/python
  3 import socket
  4 import commands
  5 import sys
  6 from thread import *
  7 
  8 
  9 HOST = '10.10.30.63'
 10 PORT = 8000
    #创建socket
 11 s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
 12 print 'Socket created!'
 13 try:
 14     s.bind((HOST,PORT))  #绑定到特定ip和端口
 15 except socket.error,msg:
 16     print('Failed to create socket!.Error code : '+ str(msg[0]) + 'message:'+ msg[1])
 17     sys.exit()
 18 
 19 print 'Socket bind success!'
 20 
 21 #监听连接
 22 s.listen(10)
 23 print 'Socket listening...'
 24 
 25 #创建新的进程来处理一个连接的通信
 26 def clientthread(conn):
 27     conn.send('Welcome.Type something and hit enter\n')
 28     while True:
 29         data = conn.recv(1024)  #The maximum amount of data to be received at once
 30         if 'GET' in data:
 31 
 32             reply = 'ok...' + data
 33         else:
 34             reply = 'fail...'
 35 
 36         if not data:
 37             break
 38         conn.sendall(reply)
 39 
 40     conn.close()
 41 
 42 
 43 
 44 while(1):
 45     conn, addr = s.accept()
 46     print 'Connected by', addr
 47     #cmd_status, cmd_result = commands.getstatusoutput(data)
 48     #if len(cmd_result.strip()) == 0:
 49     #   conn.sendall('Dond')
 50     #else:
 51     #   conn.sendall(cmd_result)
 52     #data = conn.recv(1024)
 53     #conn.sendall(data)
 54     start_new_thread(clientthread,(conn,))
 55 
 56 
 57 s.close()
```

#####Client.py
```

  1 #!/usr/bin/python
  2 import socket
  3 import sys
  4 HOST = '10.10.30.63'
  5 PORT = 8000
  6 s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  7 s.connect((HOST,PORT))
  8 
  9 message = "POST / HTTP/1.1\r\n\r\n"
 10 
 11 try:
 12     s.sendall(message) #向服务端发送消息
 13 except socket.error:
 14     print 'Send Failed!'
 15     sys.exit()
 16 
 17 reply = s.recv(4096)
 18 
 19 print reply
 20 
 21 s.close()
```

其他需求还包含：
1、客户端：
​    a.可以通过读取文件的方式发送请求
​    b.支持消息发送速度配置
​    c.支持消息类型配置(我猜是POST、GET配置)
​    d.支持内容编码配置(UTF-8、Unicode、GBK等)
2、服务端：
​    a.可计算每秒接收客户端请求数量
​    b.支持给客户端发送不同状态码
3、连接方式：支持长连接和短连接 
最终成型的模拟器大概是需要5000个消息并发，有的需求还没看明白，慢慢来吧，哎，争取这个迭代完成嗯0v0



2017-08-29更新：
我觉得自己真是太蠢了，直接起个httpserver就好了嘛，浪费那么久时间SAD
不知道这个SimpleHTTPServer经不经受得起每秒上千的轰炸







-------------------------------------------------------------
参考：*http://www.binarytides.com/python-socket-programming-tutorial/*
