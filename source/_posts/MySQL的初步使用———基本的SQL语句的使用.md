---
title: MySQL的初步使用———基本的SQL语句的使用
date: 2017-08-25 10:01:53
tags: mysql
---
   基本的Sql语句的，分为3种，**DDL**，Data Definition Language 数据库定义语言） ，DML，（Data Manipulation Language 数据操纵语言），DCL，（Data Control Language 数据库控制语言）。
     
       

数据定义语言DDL
       CRTATE database mydb(你的数据库名字);   ---在数据的使用中，关键字是不区分大小写的，这一点跟Java恰好相反，为了统一，关键字我们用大写，
       ![创建数据库](http://img.blog.csdn.net/20170131100918500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluanVuamo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
       创建好之后，我们使用 show databases; (加S 复数)
       ![这里就可以看到我们刚才创建的数据库了](http://img.blog.csdn.net/20170131101157410?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluanVuamo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
       修改使用 ALTER  删除用 DROP； 这里就不一一说明了，和创建数据库差不多的结构，
       创建好数据库之后，就要创建表了，
         ![这里写图片描述](http://img.blog.csdn.net/20170131101740339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluanVuamo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
         有这几个数据库， 使用USE mydb1;
          ![使用mydb1数据库](http://img.blog.csdn.net/20170131101853981?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluanVuamo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast) 
          创建表    CREATE table people（表名字）{ id  int primary key auto_increment  createDate};
          primary key 主键    auto_increment 自增
       查看表 
       desc peopel(表名);
       
          **数据操纵语言DML**   语句insert update delete
            添加数据 insert into people( id ,now()) values(1));
        修改表名 alter table peopel rename test
        删除表列 alter table people drop column name;
        修改表类型：  alter table people modify address char(40);
        // alter table people change address char(40);
        修改表列名：alter table people column address address1 varchar(30);
         
             添加一列update peopel set id=1,creatDate="2017_5_6" where id=0;
      
              **数据查询语言DRL**    select语句
              select * from people （查询当前的数据的所有数据）       *代表所有
              在实际开发中 应该避免使用* 因为*是通配符，编译需要时间
              select id，createDate from peopel; 与上面查询语句是一样的，
         select * 3from peopel where id=1;(where  id =1 索引)
           select * from people where createDate is null(索引为空)
           select * from people where createDate is not null(索引不为空)      