---
layout:     post              # 使用的布局（不需要改）
title:      写一个简单的Netty Server        # 标题 
subtitle:                 #副标题
date:       2018-03-07        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Java
    - Netty
---



*Server*

```
package nettytest;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelOption;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;

public class NettyServer {

    public void start(int port) throws Exception {
         //Netty4中的EventLoop 代替Netty3中的 ChannelFactory
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
         try {
             ServerBootstrap serverBootstrap = new ServerBootstrap();
             // server端采用简洁的连写方式，client端才用分段普通写法。
            serverBootstrap.group(bossGroup, workerGroup)
                      .channel(NioServerSocketChannel.class )
                      .childHandler(new ChannelInitializer<SocketChannel>() {
                            @Override
                            public void initChannel(SocketChannel ch) throws Exception {
                            	//在channel队列中添加一个handler，用于处理业务
                                ch.pipeline().addLast(new HelloServerHandler());
                           }
                      }).option(ChannelOption.SO_BACKLOG, 128)
                      .childOption(ChannelOption.SO_KEEPALIVE, true);

            ChannelFuture f = serverBootstrap.bind(port).sync();
            //程序会一直等待，直到channel关闭
            f.channel().closeFuture().sync();
        } catch (InterruptedException e) {
        } finally {
        	//关闭EventGroup，释放所有资源
            workerGroup.shutdownGracefully();
            bossGroup.shutdownGracefully();
        }
   }

    
 public static void main(String[] args) throws Exception {  
	 	NettyServer server = new NettyServer();
        //启动服务，端口为9000
        server.start(9000);  
 		}
 }  

```



*HelloServerHandler*

```
package nettytest;

import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.util.CharsetUtil;


/*
*ChannelInboundHandler:处理客户端发往服务端的报文，用于解码、读取客户端数据、进行业务处理等
*ChannelOutboundHandler:处理从服务端发往客户端的报文，用于编码、给客户端发送报文
*/
public class HelloServerHandler extends ChannelInboundHandlerAdapter {    
  
    @Override  
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {   
        ctx.write(Unpooled.copiedBuffer("Netty rocks!", CharsetUtil.UTF_8)); 
        ctx.fireChannelRead(msg);  
    }  
  
    @Override  
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {  
    	System.out.println("InboundHandler1.channelReadComplete");  
        ctx.flush();  
    }  
}  
```

