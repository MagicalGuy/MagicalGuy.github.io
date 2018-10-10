---
layout: post
title:  "Linux常见基础操作"
date:   2015-05-03 08:15:01
categories: Linux Command
tags: Linux Command
---

* content
{:toc}

## 常见

1. 通过远程工具登陆到linux后，所在的位置是当前登录用户的家目录(home directory)。
2. 家目录的符号用~表示。
3. linux的文件系统是一个树结构。
  >linux文件系统的树根成为根目录，使用符号"/"表示。
  >linux文件系统中使用符号".."表示上级目录。
  >linux文件系统中使用符号"."表示当前目录。
  >linux文件系统中，如果文件使用“.”开头，就表示隐藏文件
  >linux的路径表示方式有两种，一种是绝对路径(以”/“开头的路径)，一种是相对路径
4. 命令cd(change directory)：跳转到其他目录
5. 命令ls：查看目录下包含什么东西
  >ls  -l：显示详细信息
  >ls  -a：显示隐藏文件
6.命令pwd：显示当前所处位置的
7. 命令clear：清屏
8. 命令mkdir：创建文件夹
  >mkdir -p  ：递归创建上级文件夹
9. 命令touch：创建空白文件
10. 命令rm：删除文件
  >命令rmdir：删除文件夹
  >命令rm -rf:删除文件(夹)，不论层级多深，一概删除，并且不给出提示
12. 命令cp(copy)：复制文件，还有一个功能是创建新文件  
  >递归复制 cp -R
13. 命令mv(move): 移动文件，还有个功能是重命名
14. 重启 reboot -h now
***

## VI(vim)操作：
(1)当使用命令vi f1回车时，进入了vi编辑器内，处于只读状态(只能看不能写)；
(2)按字母”i“(insert)或者”a“(append)就可以进入编辑状态；
(3)按键盘上的”Esc“键，再按Shift+:，进入命令状态
  >如果输入命令wq，意味着保存退出；
  >如果输入命令q！，意味着不保存退出；
***

## 权限
linux的权限使用9位字符表示，rwx rwx rwx
这里面r表示读权限，w表示写权限，x表示执行权限，-表示没有权限

这9位字符，每3位一组，分为3组。
第一组表示创建者的权限；
第二组表示创建者所在组的权限；
第三组表示其他人的权限；

权限还可以使用数字表示。rwx用数字表示为4+2+1=7。
新创建文件的默认权限是644；新创建文件夹的默认权限是755.

命令chmod：修改权限，三个组分别使用u、g、o表示，赋权限使用“+”，去掉权限使用“-”。
hmod u+x f1  //表示文件f1的创建者增加执行权限
chmod o-r f1  //表示文件f1的其他人去掉读权限

***
###设置ip地址
	执行命令	service network restart
	验证:	ifconfig
### 关闭防火墙
	执行命令	service iptables stop
	验证:		service iptables status
### 关闭防火墙的自动运行
	执行命令	chkconfig iptables off
	验证:		chkconfig --list | grep iptables
### 设置主机名
	执行命令	(1)hostname 主机名
			(2)vi /etc/sysconfig/network

### ip与hostname绑定
	执行命令	vi /etc/hosts
	验证:		ping 主机名
###设置ssh免密码登陆
	执行命令	(1)ssh-keygen -t rsa
			(2)cp ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys
	验证：		ssh 主机名


## 查看命令
1. cat命令：
     功能：1）显示整个文件。
                   示例： $ cat fileName
              2）把文件串连接后传到基本输出，如将几个文件合并为一个文件或输出到屏幕。
                   示例： $ cat file1 file2 > file
     说明：把档案串连接后传到基本输出(屏幕或加 > fileName 到另一个档案)
     cat参数详解：
     -n 或 –number 由 1 开始对所有输出的行数编号
     -b 或 –number-nonblank 和 -n 相似，只不过对于空白行不编号
     -s 或 –squeeze-blank 当遇到有连续两行以上的空白行，就代换为一行的空白行
     -v 或 –show-nonprinting
2. more命令：
     以百分比的形式查看日志。    
 
3. less命令：
     跟more功能差不多，只不过less支持前后翻阅文件。
 
4. head命令：
     功能：从文本文件的头部开始查看，head 命令用于查看一个文本文件的开头部分。
     示例如下：
     head example.txt 显示文件 example.txt 的前十行内容；
     head -n 20 example.txt 显示文件 example.txt 的前二十行内容；
     head详解：
     -n      指定你想要显示文本多少行。
     -n number     这个参数选项必须是十进制的整数，它将决定在文件中的位置，以行为单位。
     -c number     这个参数选项必须是十进制的整数，它将决定在文件中的位置，以字节为单位。
5. tail命令：
     功能：tail 命令用于显示文本文件的末尾几行。
     示例如下：
     tail example.txt 显示文件 example.txt 的后十行内容；
     tail -n 20 example.txt 显示文件 example.txt 的后二十行内容；
     tail -f example.txt 显示文件 example.txt 的后十行内容并在文件内容增加后，自动显示新增的文件内容。
     tail -n 50 -f example.txt 显示文件 example.txt 的后50行内容并在文件内容增加后，自动显示新增的文件内容。
     注意：
     最后一条命令非常有用，尤其在监控日志文件时，可以在屏幕上一直显示新增的日志信息。
     tail详解：
     -b Number 从 Number 变量表示的 512 字节块位置开始读取指定文件。 
     -c Number 从 Number 变量表示的字节位置开始读取指定文件。 
     -f 如果输入文件是常规文件或如果 File 参数指定 FIFO（先进先出），
     那么 tail 命令不会在复制了输入文件的最后的指定单元后终止，而是继续
     从输入文件读取和复制额外的单元（当这些单元可用时）。如果没有指定 File 参数，
     并且标准输入是管道，则会忽略 -f 标志。tail -f 命令可用于监视另一个进程正在写入的文件的增长。 
     -k Number 从 Number 变量表示的 1KB 块位置开始读取指定文件。 
     -m Number 从 Number 变量表示的多字节字符位置开始读取指定文件。使用该标志提供在单字节和双字节字符代码集环境中的一致结果。 
      -n Number 从首行或末行位置来读取指定文件，位置由 Number 变量的符号（+ 或 - 或无）表示，并通过行号 Number 进行位移。 
       -r 从文件末尾以逆序方式显示输出。-r 标志的缺省值是以逆序方式显示整个文件。如果文件大于 20,480 字节，那么-r标志只显示最后的 20,480 字节。 -r 标志只有
   与 -n 标志一起时才有效。否则，就会将其忽略。