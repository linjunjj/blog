---
title: Netty学习笔记（1）--Netty开发环境的搭建
date: 2017-09-26 22:17:30
tags:
---
# Netty学习笔记（1）--Nett开发环境的搭建
### 下载Neety软件包
访问netty的官网http://netty.io/,从【download】这个按钮点击下载软件包，
![](http://owxnr6n7r.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-09-27%2018.01.15.png)
 下载之后，就解压
 ### 搭建netty应用工程
 
 这里我们使用的编辑器是IDEA，
 ### （1）导入jar的方式来新建Netty工程
 新建工程，将lib下的jar包加入新的工程中，
 ![](http://owxnr6n7r.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-09-27%2021.08.06.png)
 这样就搭建初始环境了，
 
 ### （2）通过Maven的方式新建Netty工程
 将刚刚生成的项目包导入到， java-idea 中，具体的操作流程如下图所示
   
 选择 New --> Module from Existing Sources 
![](http://blog.chinaunix.net/attachment/201506/20/28595538_1434756877sVXh.jpg)
 ![](http://blog.chinaunix.net/attachment/201506/20/28595538_1434756876Z6U6.jpg)
 ![](http://blog.chinaunix.net/attachment/201506/20/28595538_1434756876PRUv.jpg)
   ![](http://blog.chinaunix.net/attachment/201506/20/28595538_1434756876rnoR.jpg) 
 ![](http://blog.chinaunix.net/attachment/201506/20/28595538_14347568775449.jpg)
 
 
 
 
 

 
 4. 好的，现在项目包已经被导入进 IDE 开发环境中了， 我们来修改一下 pom.xml 的文件，在依赖标签中，
   按照一定格式要求，写入对 netty 软件的需求。Java- idea 根据 pom.xml 文件来自动从网上下载对应的资源，
    并将该资源存放到本地的资源库中，在我的计算机中，maven 的资源库路径位于 /root/.m2/ 下面
   ![](http://blog.chinaunix.net/attachment/201506/20/28595538_1434756877UQn2.jpg)
 
 5. 在执行上面操作之后，就会发现在 IDE 下面有下载 netty 软件包的进度条，等其完成之后，我们打开位于
     src/main/App.java ， 在package .... 一行下面写入 
   
     import io.netty.bootstrap.Boostrap ; 
 
   语句检测一下编译 语句，检测一下编译环境是否会报错，如果不会报错的话，则说明该 netty 文件包已经成功的通过修改 maven 文件而被引入到该项目开发环境中的.
 ![](http://blog.chinaunix.net/attachment/201506/20/28595538_1434756877JjkN.jpg)
 
 
 add 6/21
 如果想要增加 spring-framework 依赖的话
 在 pom.xml 中添加
 
 点击(此处)折叠或打开
 <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>3.1.1.RELEASE</version>
     </dependency>
 IDE 将会自动的从官网"org.springframwork" 中下载对应版本的文件包，并将其加载到当前的项目中
 