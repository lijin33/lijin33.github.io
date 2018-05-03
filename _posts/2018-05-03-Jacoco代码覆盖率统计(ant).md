---
layout:     post              # 使用的布局（不需要改）
title:      Jacoco代码覆盖率统计(ant)        # 标题 
subtitle:                 #副标题
date:       2018-05-03       # 时间
author:     Lacia           # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - APM
---

#### Jacoco代码覆盖率统计(ant)

这篇相当于是对前面一篇的补充，记录一下在普通环境下进行java代码覆盖率统计的方法。



##### 准备

1. Java环境

2. apache-ant

3. 待测程序

4. jacoco agent

   

##### 修改build.xml文件

修改`apache-ant-xxx/bin`目录下的`build.xml`文件：

```
<?xml version="1.0" ?>
<project name="testCodeCoveage"  basedir="/home/jxsw/apache-ant-1.10.1/bin/report" xmlns:jacoco="antlib:org.jacoco.ant" xmlns:sonar="antlib:org.sonar.ant" default="all">
  <!--Jacoco的安装路径-->
  <property name="jacocoantPath" value="/home/jxsw/jacoco-0.7.9/lib/jacocoant.jar"/>
  <!--最终生成.exec文件的路径，Jacoco就是根据这个文件生成最终的报告的-->
  <property name="jacocoexecPath" value="/home/jxsw/jacoco_file/jacoco.exec"/>
    <!--生成覆盖率报告的路径-->
  <property name="reportfolderPath" value="${basedir}/report/"/>
  <!--远程服务的ip地址，如有多个，可设置多个，name需修改-->
  <property name="server_ip" value="10.10.30.66"/>

  <!--待测程序.class文件路径-->
  <property name="waterommpClasspath" value="/home/jxsw2/ommp/water-ommp-release-0.0.1/lib/water-ommp-release-0.0.3/com/emrubik/water/ommp" />         
  
  <!--Jacoco所在目录-->
  <taskdef uri="antlib:org.jacoco.ant" resource="org/jacoco/ant/antlib.xml">
      <classpath path="${jacocoantPath}" />
  </taskdef>
  
  <!--merge task，当有多个待测程序时，生成报告前需将所有.exec文件merge成一个-->
  <target name="merge" depends="dump">
        <jacoco:merge destfile="jacoco.exec">
       <fileset dir="${basedir}" includes="*.exec"/>
        </jacoco:merge>
  </target>

  <!--dump任务:
      根据前面配置的ip地址，和端口号，访问目标服务，并生成.exec文件。-->

  <target name="dump">
        <!-- reset="true"是指在dump完成之后，重置jvm中的覆盖率数据为空。append="true"是指dump出来的exec文件为增量方式 -->
        <jacoco:dump address="${server_ip}" reset="false" destfile="${basedir}/waterommp.exec" port="15501" append="true"/>
  </target>


  <!--report任务:
      根据前面配置的源代码路径和.class文件路径，
      根据dump后，生成的.exec文件，生成最终的html覆盖率报告。-->
  <target name="report">
      <delete dir="${reportfolderPath}" />
      <mkdir dir="${reportfolderPath}" />

      <jacoco:report>
          <executiondata>
                 <file file="${basedir}/jacoco.exec" />

          </executiondata>

          <structure name="JaCoCo Report">

          <group name="waterommp related">
                  <classfiles>
                      <fileset dir="${waterommpClasspath}" >

                      </fileset>
                  </classfiles>
       <!--  exclude中为生成报告时排除的class   -->

<!--              <sourcefiles encoding="gbk">
                      <fileset dir="${mcmSrcpath}" >
                        <exclude name="**/north/control/iosconstans/*.java"/>
                      </fileset>
                  </sourcefiles>
-->
              </group>
          </structure>

          <html destdir="${reportfolderPath}/report" encoding="utf-8" />
          <csv destfile="${reportfolderPath}/report.csv"   />
          <xml destfile="${reportfolderPath}/report.xml" />

      </jacoco:report>
  </target>
  <target name="all" />

</project>

```



##### 修改程序的启动脚本

- tomcat

  ```
  CATALINA_OPTS="-javaagent:/home/jxsw/jacoco-0.7.9/lib/jacocoagent.jar=includes=*,output=tcpserver,port=15501,address=10.10.30.66,append=true"
  ```

  port即为`ant.xml`中dump时需要配置的监听端口

- 其他

  启动脚本中添加：

  ```
  -javaagent:/home/jxsw/jacoco-0.7.9/lib/jacocoagent.jar=destfile=/home/jxsw/jacoco_file/waterommp.exec,output=tcpserver,port=15502,address=10.10.30.66
  ```

  

##### 开始

重启待测程序，执行一系列操作之后在`apache-ant-xxx/bin`目录下执行`ant dump`、`ant merge`、`ant report`命令，如下图：

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180503/9m3463C5E7.png?imageslim)

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180503/g7LG0JamFC.png?imageslim)

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180503/6jb7443952.png?imageslim)



在`build.xml`中配置的`reportfolderPath`中即可见生成的报告啦，用ftp拖到本地就可以看啦ovo

![mark](http://owl3le8ji.bkt.clouddn.com/blog/180503/d3iGB7DmIF.png?imageslim)

