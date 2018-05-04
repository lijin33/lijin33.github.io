---
layout:     post              # 使用的布局（不需要改）
title:      使用pykafka库测试kafka       # 标题 
subtitle:                  #副标题
date:       2018-05-04        # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Python
    - kafka
---

#### 使用pykafka库测试kafka



##### 生产者生产数据

```
from pykafka import KafkaClient
 
client = KafkaClient(hosts ="10.10.30.66:9092") #可接受多个client
#查看所有的topic
client.topics
print(client.topics)

#选择一个topic
topic = client.topics[b'thresholdAlarm']
message ='{"alarmOid":"1.1.1.4","deviceId":"d92c66617162477da0137e139712e665","deviceName":"乐平水表001","deviceType":"edcd7a32-a744-41db-af2e-b5e95cf249b4","org":"1"}'

producer=topic.get_producer()
producer.produce(bytes(message, encoding = "utf8"))
#关闭链接，否则报错
producer.stop()   
```

在kafka目录下执行`./kafka-console-consumer.sh --zookeeper 10.10.30.66:2181 --topic normalAlarm --from-beginning`，即可看见刚刚发出的消息。

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180504/E7AH31fg8J.png?imageslim)



##### 消费数据

```
from pykafka import KafkaClient
 
client = KafkaClient(hosts ="10.10.30.66:9092")
#查看所有的topic
#client.topics
print(client.topics)

topic = client.topics[b'thresholdAlarm']

consumer = topic.get_simple_consumer(consumer_group=b'test', auto_commit_enable=True, auto_commit_interval_ms=1, consumer_id=b'test')  
for message in consumer:  
    if message is not None:  
        print(message.offset, message.value)

print("+++++++++++++++++++++++++++++")
```

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180504/hII1BmHfAb.png?imageslim)