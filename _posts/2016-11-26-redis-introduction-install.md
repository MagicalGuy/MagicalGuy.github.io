---
layout: post
title:  "Redis介绍与Linux下安装"
categories: Redis Linux
tags:  Redis Linux
author: lst
---

* content
{:toc}

## Redis介绍

1. Redis:REmote DIctionary Server(远程字典服务器)
是完全开源免费的，用C语言编写的，遵守BSD协议，
是一个高性能的(key/value)分布式内存数据库，基于内存运行
并支持持久化的NoSQL数据库，是当前最热门的NoSql数据库之一,
也被人们称为数据结构服务器


2. Redis 与其他 key - value 缓存产品有以下三个特点:
Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储
Redis支持数据的备份，即master-slave模式的数据备份

3. 作用:
内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务
取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面
模拟类似于HttpSession这种需要设定过期时间的功能
发布、订阅消息系统
定时器、计数器

[下载地址1](Http://redis.io/)
[下载地址2](Http://www.redis.cn/)



## Linux环境下安装

安装步骤：（由于企业里面做Redis开发，99%都是Linux版的运用和安装）
下载获得redis-3.0.4.tar.gz后将它放入我们的Linux目录/opt
/opt目录下，解压命令:tar -zxvf redis-3.0.4.tar.gz
解压完成后出现文件夹：redis-3.0.4

![redis1](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/redis3.0.4.gif)

进入目录:cd redis-3.0.4

![redis2](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/jinru.gif)

在redis-3.0.4目录下执行make命令

运行make命令时出现的错误解析：要安装gcc

gcc是linux下的一个编译程序，是C程序的编译工具。
* 连接网络方式安装gcc：yum install gcc-c++

* 断网下安装gcc：
分别执行如下命令

![redis11](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/gcc.gif)

什么情况下需要二次make
如果Jemalloc/jemalloc.h：没有那个文件或目录
运行make distclean之后再make

![redis3](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/makeagain.gif)


Redis Test(可以不用执行)

![redis4](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/maketest.gif)


测试需要安装TCL

[下载TCL的网址：](http://www.linuxfromscratch.org/blfs/view/cvs/general/tcl.html)

安装TCL

联网安装TCL

![TCL](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/tcl.gif)


如果make完成后继续执行make install

![redis5](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/makeinstall.gif)


查看默认安装目录：usr/local/bin

![redis6](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/usrlocalbin.gif)

Redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何,服务启动起来后执行
Redis-check-aof：修复有问题的AOF文件
Redis-check-dump：修复有问题的dump.rdb文件
Redis-cli：客户端，操作入口
Redis-sentinel：redis集群使用
Redis-server：Redis服务器启动命令




![redis7](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/qidong.gif)

修改redis.conf文件将里面的daemonize no 改成 yes，让服务在后台启动
将默认的redis.conf拷贝到自己定义好的一个路径下，比如/myconf
启动

![redis12](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/qidong1.gif)


连通测试

![redis10](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/liantong.gif)

/usr/local/bin目录下运行redis-server，运行拷贝出存放了自定义conf文件目录下的redis.conf文件



helloworld例子：

![redis8](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/helloworld.gif)

关闭

![redis9](https://raw.githubusercontent.com/MagicalGuy/MyBlogPicture/master/oldpicture/guanbi.gif)

单实例关闭：redis-cli shutdown
多实例关闭，指定端口关闭:redis-cli -p 6379 shutdown

