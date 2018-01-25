### CDH

##### 向群集中添加服务时，数据库连接失败：可以找到数据库但用户名密码登陆失败

原因：数据库用户权限问题

解决方法：

- 进入mysql

  ```
  mqsql -uroot -ppwd
  ```

  修改用户权限

  ```
  grant all privileges on *.* to 'root'@'test75' identified by '{psd}' with grant option;
  flush privileges;
  ```

同时发现未将mysql-connector放入指定路径中，于是在所有节点中添加mysql connector。

```
cp mysql-connector-java-6.0.2.jar /opt/cm-5.12.1/share/cmf/lib/
```



##### jdk版本问题：

出现了一点小尴尬

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/2B4jk6Dd5K.png?imageslim)

而使用的jdk版本恰好是……

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/h2CgimdiJ3.png?imageslim)

将jdk版本换为1.8.0_121，但是之后cm报错，查看日志时发现虽然原来的jdk1.8.0_60已经被我删除，而且环境变量指向的jdk版本也已经变更，cm还是在找原来的1.8.0_60版本。无奈之下在主机配置中添加了JAVA配置：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/8CKmdDcE9b.png?imageslim)

添加配置后不再报错。由于其他主机一开始安装的jdk版本都是1.8.0_60，所以在其他主机配置中也配置了java路径。



P.S: 之后安装oozie的时候出现了问题，怀疑与jdk版本有关嗯…（待考）



##### 主机DNS查找错误

原因是未在hosts文件中配置其他主机的ip和主机名称，配置后不再报错。



##### 文件权限问题

这个问题现在还有点迷糊。

在安装服务的过程中出现过几次文件权限问题，修改出现问题的文件夹权限即可解决。

cluster配置问题：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/jKEKj3j45h.png?imageslim)

虽然报错了但是看起来并未产生什么影响……



##### HDFS配置问题：副本不足的块

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/HegB68Gai9.png?imageslim)



待考，现在不知道咋解决啊



##### 一个严重的问题

群集cluster1有两台主机，一台为namenode：test75，一台为datanode：node1。

当前状态，所有服务的角色都在test75上，原因有些尴尬……

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/BjeGF37hhl.png?imageslim)

在node1上添加HDFS的datanode角色报错：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/2bGl3dJ80a.png?imageslim)

详细日志：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180124/aI2F5eDglL.png?imageslim)





如果把一些角色分配给node1，部署的时候会报错：CDH5 Failed to deploy client configuration.仅完成 1/3 个步骤。首个失败：主机 node (id=2) 上的客户端配置 (id=1) 已使用 1 退出，而预期值为 0.

困扰了两天，各种google，找到的一些资料提到了以下几个原因：

1、java环境配置问题

2、组件配置问题



##### 部署HDFS时node1报错：Access denied for user hdfs. Superuser privilege is required

修改hdfs配置文件，路径为`/etc/hadoop/conf/hdfs-site.xml`，添加

```
  <property>
    <name>dfs.permissions</name>
    <value>false</value>
  </property>
```







##### hdfs状态问题-副本不足的块













