---
layout: post
title:  "MyBatis+Spring+SpringMVC  整合思路"
date:   2015-04-10 00:12:00
categories: MyBatis Spring SpringMVC
tags: MyBatis Spring SpringMVC 整合
---

* content
{:toc}


## MyBatis Spring SpringMVC 整合思路和注意事项


### 相关引用jar包

* MyBatis核心和MyBatis依赖包
* MyBatis和spring整合包
* Spring的jar（包括springmvc的jar包）
* 数据库驱动包
* 第三方数据库连接池c3p0等


### 配置文件

* db.properties—数据库连接参数
* log4j.properties---日志 配置文件
* mybatis/SqlMapConfig.xml---mybatis全局配置文件
* spring/springmvc.xml----------springmvc的全局配置文件
* spring/applicationContext.xml---spring配置文件（配置公用内容：数据源、事务）
* spring/ applicationContext- dao.xml---spring和mybatis整合的配置（SqlSessionFactory、mapper配置）
* spring/ applicationContext-service.xml---配置业务接口

![整合](http://oms5nhjmw.bkt.clouddn.com/spring_mybatis_springmvc.jpg)

### 整合步骤：
1. 整合持久层
Mybatis和spring整合
整合目标：mapper创建由spring来管理
SqlSessionFactory由spring管理（设置单例 ）

* 方法一：通过spring开发原始dao
applicationContext.xml配置数据源，SqlMapConfig.xml配置mapper
在applicationContext-dao.xml配置SqlSessionFactory,从mybatis和spring的整合包找SqlSessionFactory
需要写dao接口和dao实现类。编写dao的实现类，继承SqlSessionDaoSupport
定义实体类xml，且在SqlMapConfig.xml中配置。
在applicationContext-dao.xml配置dao
由于企业中原来使用的是ibatis，很多使用原始dao开发方法。

*方法二：通过spring生成mapper动态代理
开发mapper接口如UserMapper.java   UserMapper.xml
在applicationContext-dao.xml配置mapper(让spring生成动态代理)
需要在spring容器中配置每个mapper接口，比较麻烦。使用扫描器来解决。
使用mapper批量扫描器自动创建代理对象,在applicationContext-dao.xml中配置扫描器
由于使用spring和mybatis整合的mapper扫描器，原来在SqlMapConifg.xml配置mapper项就可以省略了。
使用扫描器简单方便，最终使用mapper扫描器方式。


2. 整合业务层
Spring管理service
整合目标：Service通过spring调用mapper,由spring进行事务控制

开发service接口,为了方便系统维护企业开发中建议使用配置方式，在applicationContext-service.xml配置service。
加载spring容器，加载配置文件包括：applicationContext-service.xml,applicationContext-dao.xml,applicationContext.xml
为了规范程序员编码，采用声明式事务配置方式。在applicationContext.xml配置事务管理



3. 整合控制层
因为springmvc是spring一个模块，只要加入jar包，配置springmvc.xml和web.xml即可。
整合目标：action中通过spring调用service

配置Springmvc.xml
Springmvc.xml,处理器映射器、处理器适配器(可以mvc注解驱动规则这两个处理器映射器、处理器适配器)，视图解析器。
配置action的组件扫描。
配置前端控制器,在web.xml配置

在web.xml加载spring容器,通过统配符加载spring配置文件,
配置文件包括：applicationContext-service.xml,applicationContext-dao.xml,applicationContext.xml

