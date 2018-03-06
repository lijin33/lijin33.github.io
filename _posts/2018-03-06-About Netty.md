Server



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
         // EventLoop 代替原来的 ChannelFactory
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
                                ch.pipeline().addLast(new HelloServerHandler());
                           }
                      }).option(ChannelOption.SO_BACKLOG, 128)
                      .childOption(ChannelOption.SO_KEEPALIVE, true);

            ChannelFuture f = serverBootstrap.bind(port).sync();
            f.channel().closeFuture().sync();
        } catch (InterruptedException e) {
        } finally {
            workerGroup.shutdownGracefully();
            bossGroup.shutdownGracefully();
        }
   }

    
 public static void main(String[] args) throws Exception {  
	 	NettyServer server = new NettyServer();  
        server.start(9000);  
 		}
 }  

```







```
package nettytest;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;

  
public class HelloServerHandler extends ChannelInboundHandlerAdapter {  
    //private static Logger  logger  = LoggerFactory.getLogger(HelloServerHandler.class);  
  
    @Override  
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {  
       System.out.println("InboundHandler1.channelRead: ctx :" + ctx);  
        // 通知执行下一个InboundHandler  
        ctx.fireChannelRead(msg);  
    }  
  
    @Override  
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {  
    	System.out.println("InboundHandler1.channelReadComplete");  
        ctx.flush();  
    }  
}  
```

