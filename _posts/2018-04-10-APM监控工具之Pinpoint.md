---
layout:     post              # 使用的布局（不需要改）
title:      APM监控工具之Pinpoint初探        # 标题 
subtitle:                 #副标题
date:       2018-04-10       # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - APM
---

## APM监控工具之Pinpoint初探

#### 简介

- [Pinpoint](https://github.com/naver/pinpoint) 是一个开源的APM监控工具，我们可以通过pinpoint实时跟踪应用之间的调用、程序的响应时间以及服务器资源使用状态，可以在分布式环境中为没个调用生成代码级别的可视图并定位瓶颈点和失败点。

- Pinpoint参考的是Google的Dapper，参考文档:*[http://bigbully.github.io/Dapper-translation/](http://bigbully.github.io/Dapper-translation/)*

- Pinpoint目前支持的模块：

  ```
  JDK 6+
  Tomcat 6/7/8, Jetty 8/9, JBoss EAP 6, Resin 4, Websphere 6/7/8, Vertx 3.3/3.4/3.5
  Spring, Spring Boot (Embedded Tomcat, Jetty)
  Apache HTTP Client 3.x/4.x, JDK HttpConnector, GoogleHttpClient, OkHttpClient, NingAsyncHttpClient
  Thrift Client, Thrift Service, DUBBO PROVIDER, DUBBO CONSUMER
  ActiveMQ, RabbitMQ
  MySQL, Oracle, MSSQL, CUBRID,POSTGRESQL, MARIA
  Arcus, Memcached, Redis, CASSANDRA
  iBATIS, MyBatis
  DBCP, DBCP2, HIKARICP
  gson, Jackson, Json Lib
  log4j, Logback
  ```

  ​

#### 安装及部署

安装pinpoint需要：

0、JAVA环境

1、Hbase

2、pinpoint-collector、pinpoint-web、pinpoint-agent

3、mysql

就现有环境来说，从安装Hbase开始操作

##### 安装Hbase

1、下载[Hbase](http://apache.mirror.cdnetworks.com/hbase/)

2、解压tar包

3、修改配置文件

hbase-env.sh

```
#使用Hbase自带的zookeeper，如使用外置zookeeper需置为false
export HBASE_MANAGES_ZK=true
#配置JAVA环境变量
export JAVA_HOME=/usr/java/jdk1.8.0_121
```

hbase-site.xml

```
#添加以下配置
	<property>
        <name>zookeeper.znode.parent</name>
        <value>/hbase</value>
    </property>

```

4、启动Hbase

```
./start-hbase.sh
```

5、初始化Hbase表

这个步骤需要pinpoint源码中的`hbase-create.hbase`脚本。

下载源码后，在Hbase的bin目录下运行：

`./hbase shell /home/liang/pinpoint/pp-src/hbase/scripts/hbase-create.hbase `

理想的情况是运行成功，可以在hbase shell中通过`status 'detailed'`查看已建立的表的情况。



##### 配置pinpoint-collector

将pinpoint-collector解压后放入web容器中，本次实验中使用的是tomcat。

进入配置文件目录`/home/liang/pinpoint/collector-pinpoint/webapps/pinpoint-collector-1.7.2-SNAPSHOT/WEB-INF/classes`，修改`hbase.properties`文件：

```
#Hbase所在的主机名
hbase.client.host=k8s-slave3
hbase.client.port=2181
```

修改tomcat启动端口，以免发生端口冲突事件。

启动tomcat。



##### 配置pinpoint-web

将pinpoint-web解压后放入web容器中，本次实验中使用的是tomcat。

进入配置文件目录`/home/liang/pinpoint/web-pinpoint/webapps/pinpoint-web-1.7.2-SNAPSHOT/WEB-INF/classes`，修改`hbase.properties`文件：

```
#Hbase所在的主机名
hbase.client.host=k8s-slave3
hbase.client.port=2181
```

修改tomcat启动端口，以免发生端口冲突事件。

启动tomcat。



##### 部署pinpoint-agent

将pinpoint-agent copy至需要监控的程序所在服务器中，修改程序的启动脚本。

1. Tomcat的情况，在`catalina.sh`中添加以下几行：

   ```
   CATALINA_OPTS="$CATALINA_OPTS -javaagent:/home/liuhy/pinpoint/pinpoint-bootstrap-1.7.2-SNAPSHOT.jar"
   CATALINA_OPTS="$CATALINA_OPTS -Dpinpoint.agentId=0000001"
   CATALINA_OPTS="$CATALINA_OPTS -Dpinpoint.applicationName=10.10.30.63_emp"	
   ```

2. Springboot启动，在启动脚本中添加：

   ```
   -javaagent:/home/liuhy/pinpoint/pinpoint-bootstrap-1.7.2-SNAPSHOT.jar 
   -Dpinpoint.agentId=0000002 
   -Dpinpoint.applicationName=10.10.30.63_mdm
   ```

   `Dpinpoint.agentId`为该程序在pinpoint中的编号，唯一且不可重复，一个agentId对应一个JVM。

   `Dpinpoint.applicationName`相同应用的applicationName相同，agentId不同。如mdm有多个实例，则多个实例的applicationName都是mdm，但agentId需不同。

   ​

修改pinpoint-agent中的pinpoint.config配置文件：

```
  5 ###########################################################
  6 # Collector server                                        # 
  7 ###########################################################
  8 #设置collector所在服务器的ip，本次实验中collector的ip为10.10.30.64
  9 profiler.collector.ip=10.10.30.64
```



修改启动脚本后重启服务，即可在pinpoint-web中看到该服务的当前状态和性能指标，如下图：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180410/A9FBdD2fGi.png?imageslim)



在多个服务中部署pinpoint：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180410/8L9mDL5fh9.png?imageslim)



#### 查看系统资源使用情况

##### 查看单个agent的实时情况

打开Inspector，选择要查看的应用的agentId，即可查看该应用的JVM参数及所在服务器资源使用情况：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180410/gE47FEECaG.png?imageslim)

P.S： *[Application Inspector](http://naver.github.io/pinpoint/applicationinspector.html)*需要进一步搭建、配置Flink环境，待考。



#### 定位系统异常

在本次实验中，EMM portal报出一个异常，通过框选异常区域可以查看该时间段所有的请求及详细信息，由此可快速定位问题。

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180410/Lkj5h3Jjgk.png?imageslim)



![mark](http://owl3le8ji.bkt.clouddn.com/blog/180410/ibKbkjKBck.png?imageslim)



#### 告警监控

在Pinpoint中可以设定告警规则，实时对程序进行监控，如触发告警则发送邮件。

该功能需要实现额外逻辑，待考。

About Alarm：*[http://naver.github.io/pinpoint/alarm.html](http://naver.github.io/pinpoint/alarm.html)*

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180410/FGbKh8Ae02.png?imageslim)



#### Pinpoint With Docker

Pinpoint-collector和Pinpoint-web均可做成docker镜像

**参考**：

* *[https://blog.csdn.net/jinzhencs/article/details/54346299](https://blog.csdn.net/jinzhencs/article/details/54346299)*
* *[https://yous.be/2015/05/05/using-pinpoint-with-docker/](https://yous.be/2015/05/05/using-pinpoint-with-docker/)*
* *https://github.com/yous/pinpoint-docker*




#### Application Inspector部署

Application Inspector在我的理解看来就是如果一个application是集群的，可以通过inspector查看相同application name的应用的所有资源使用情况。

经过几天的煎熬，终于把Application Inspector弄出来了！



##### 编译pinpoint-flink-job.jar

从编译开始就遭遇了这样那样的困难 __(:з」∠)_，一定要下pinpoint源码才行。

编译之前要修改pp-src中的配置文件：

- `pinpoint-flink.properties`，路径在`pp-src/flink/src/main/resources`

  ```
      flink.cluster.enable=true
      #zookeeper在本机
      flink.cluster.zookeeper.address=k8s-slave3
      flink.cluster.zookeeper.sessiontimeout=3000
      flink.cluster.zookeeper.retry.interval=5000
      flink.cluster.tcp.port=19994
      flink.StreamExecutionEnvironment=server
      flink.sourceFunction.Parallel=1
  ```


- `hbase.properties`，路径同上

  ```
      #hbase在本机
      hbase.client.host=k8s-slave3
      hbase.client.port=2181
  ```

  ​

然后就开始痛苦的编译！在pp-src路径下`./mvnw install -Dmaven.test.skip=true`

在maven setting.xml中加入repository路径之后拒绝连接的问题得以改善，但是仍然重复编译了四五次才最终success！好几天电脑都没关*___(:з」∠)__*

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180418/2hE6lflh1g.png?imageslim)



![mark](http://owl3le8ji.bkt.clouddn.com/blog/180418/dAI0hl0GeB.png?imageslim)

最后终于编译成功了！

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180418/HikE4cb0JB.png?imageslim)

编译pinpoint必须要配置JAVA6、JAVA7和JAVA8的环境变量嗯。

从编译之后一步一步来，每个步骤也都是遇到了很多坑。



##### Hbase和Zookeeper

因为Flink也要用到zookeeper，所以就打算自己搭一个zookeeper，让hbase也用这个外置的zookeeper。然后就改了hbase的配置文件`hbase-env.sh`:

```
export HBASE_MANAGES_ZK=false
```

重启hbase时仍然报错，各种查了之后，修改`hbase.site.xml`如下：

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>zookeeper.znode.parent</name>
    <value>/hbase</value>
  </property>
</configuration>
```

同时对zookeeper的配置文件也做了一些修改：

复制zoo_sample.cfg并命名为zoo.cfg：

```
dataDir=/usr/hadoop/zookeeper-3.4.6/data
# the port at which the clients will connect
clientPort=2181
```

然后在zookeeper根目录下创建data文件夹，`cat 1>myid`。这一步骤以后再补充。

然后启动hbase就可以啦ovo



#####  Flink版本问题与部署

一开始抱着什么都用最新的的想法使用了flink-1.4，后来看pinpoint github上的问答，1.4未经过验证且存在问题，推荐使用1.3.1版本，于是下了1.3.1版本。

配置文件并没有改动很大，修改了`flink-conf.yaml`：

```
# The number of task slots that each TaskManager offers. Each slot runs one parallel pipeline.
taskmanager.numberOfTaskSlots: 3
```

默认值是1，后来测试的时候发现slot数为1时会报slot不够用的问题，于是改成了3。

启动后可打开`http://10.10.30.64:8081`查看到flink dashboard，其中10.10.30.64为flink所在服务器ip。



##### 执行pinpoint-flink-job

在flink目录下执行已经编译好的jar包：

```
./flink run /home/liang/pinpoint/pp-src/flink/target/pinpoint-flink-job-1.8.0-SNAPSHOT.jar
```

正确启动后可以在flink dashboard中查看到这个job，运行状态为running：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180418/c7FmH3mk3k.png?imageslim)



##### 修改pinpoint collector配置文件

如[*official-guide*](http://naver.github.io/pinpoint/applicationinspector.html)所说，修改了几处配置文件，实际上有些出入。

修改` pinpoint-collector.properties`：

```
    flink.cluster.enable=true
    flink.cluster.zookeeper.address=k8s-slave3
    flink.cluster.zookeeper.sessiontimeout=3000
```

重启collector后报出`TcpDataSender is null`问题，于是在配置文件中启用tcp receiver

修改` pinpoint-collector.properties`：

```
collector.receiver.stat.tcp=true
collector.receiver.stat.tcp.ip=0.0.0.0
collector.receiver.stat.tcp.port=9995
collector.receiver.span.tcp=true
collector.receiver.span.tcp.ip=0.0.0.0
collector.receiver.span.tcp.port=9996
```

重启后不再报错。猜测应该可以设置成udp的，待考。



##### 修改pinpoint web配置文件

如[*official-guide*](http://naver.github.io/pinpoint/applicationinspector.html)所说，修改了几处配置文件。

修改`pinpoint-web.properties`：

```
config.show.applicationStat=true
```

修改`batch.properties`：

```
#flink server所在服务器
batch.flink.server=k8s-slave3
```

修改`applicationContext-batch-schedule.xml`，可设置各job的执行频率：

```
<task:scheduled-tasks scheduler="scheduler">
	...
	<task:scheduled ref="batchJobLauncher" method="flinkCheckJob" cron="0 0/10 * * * *" />
</task:scheduled-tasks>
```



现在打开pinpoint web，选择一个application，点击Inspector就可查看Inspector了

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180418/hec1GfAmjh.png?imageslim)



![mark](http://owl3le8ji.bkt.clouddn.com/blog/180418/98A2Km9Ebj.png?imageslim)




#### 总结

Pinpoint可以对分布式系统进行实时监控，也可以用作性能测试辅助。

在Pinpoint上还存在很多可能性，待考，待更新。

当前一个可用的pinpoint console：*[http://10.10.30.64:28080/pinpoint-web-1.7.2-SNAPSHOT](http://10.10.30.64:28080/pinpoint-web-1.7.2-SNAPSHOT)*，User Group部分还存在一些问题，推测与Mysql环境有关，待解决。