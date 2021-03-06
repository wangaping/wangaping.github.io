---
layout: post
title: "Mysql实战原理一"
subtitle: 'mysql基本结构&一条sql执行顺序'
author: "Wbs"
header-style: text
tags:
  - Mysql
---

首先说下mysql的基本结构主要包括两个方面

> * server层
> * 引擎层

而server层具体又包括

> * 连接器 
> * 查询缓存
> * 分析器
> * 解析器
> * 优化器
> * 执行器 <br/>
> * 还包括一些内置函数(日期、时间、加密等),存储过程、试图、触发器等


引擎层主要是负责存储数据,提供读写接口
引擎具体分类为mysql官方(如MyISAM)和第三方(常用的InnoDB mysql 5.5版本以后默认引擎)

### 一条语句的执行顺序大致如下

![sql执行图](/img/mysql/mysql-1-09-23.jpg "图片title")


下面具体介绍下每个组件的功能

### 连接器

 * 建立连接
   > 当客户端发起连接请求时 连接器在此时就已经开始对用户名和密码开始做验证了
   > 如果用户名不对则发出"Access denied for user"的错误<br/>
 * 获取权限
   >如果验证通过则进行用户权限加载(此时加载的权限供后面所有的操作使用 即使你使用其他管理员账号   	修改了登录账号的权限也只会在下次生效)
 * 维持和管理连接 
  > 如果登录没有操作 此次连接会进入空闲状态可以通过show processlist查看<br/> 
  > 如果太长时间没有操作将会自动断开连接 默认是8个小时 可以通过wait_timeout控制
	
### 查询缓存
当一条selct语句执行时,mysql首先会去查询缓存,select的sql当做key,结果作为value
**如果缓存命中,则接下来的步骤都不会执行,直接返回查询结果**<br/>

一般不推荐使用缓存,因为缓存命中率太低。<br/>

真正的数据业务对一张表的更新肯定会很频繁,当修改了一张表的数据时,那么该表上的所有缓存都将会失效。(一般建议对不经常修改的表做缓存功能)

***mysql在8.0版本已经取消了缓存***



### 分析器
* 词法分析
  > 分析你的sql条语句 识别出关键词
* 语法分析
  > 分析你的语句是否符合规范 <br/> 
  > 表名和字段名称的验证和解析
  
  

### 优化器

优化sql的执行<br/>
比如说涉及到索引或者连表查询优化器会选择执行策略
> eg: select * from t1 join t2 using(ID)  where t1.c=10 and t2.d=20;<br/>
> 此时可能会先查询t1 也可会先查询t2 可能会导致查询效率不同,优化器在此时就会做出选择


### 执行器
* 权限验证
* 执行引擎接口进行查询 









