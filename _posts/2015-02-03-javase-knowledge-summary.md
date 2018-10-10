---
layout: post
title:  "Knowledge summary of Javase"
date:   2015-02-03 13:14:36
categories: Javase
tags: Javase Generics Reflection IO Collection Map Thread Database Socket
---

* content
{:toc}

本文将简单扼要地提到javase的主要知识基础点，以便各位初学者复习！！！


## 对象的初始化顺序
首先执行父类静态的内容，父类静态的内容执行完毕后，
接着去执行子类的静态的内容，当子类的静态内容执行完毕之后，再去看父类有没有非静态代码块，
如果有就执行父类的非静态代码块，父类的非静态代码块执行完毕，接着执行父类的构造方法；
父类的构造方法执行完毕之后，它接着去看子类有没有非静态代码块，如果有就执行子类的非静态代码块。
子类的非静态代码块执行完毕再去执行子类的构造方法。总之一句话，静态代码块内容先执行，
接着执行父类非静态代码块和构造方法，然后执行子类非静态代码块和构造方法。 

## 修饰符作用域

| 作用域    | 当前类 |  同一包 | 子孙类  | 其他包 |
| --------- |:------:|:-------:|:-------:| ------:|
|  public   |   1    |    1    |   1     |   1    |
| protected |   1    |     1   |    1    |    0   |
| friendly  |   1    |     1   |    0    |    0   |   没有任何修饰符则默认为friendly
| private   |   1    |     0   |    0    |   0    |


## Serializable
作用：将对象转为二进制流用于存储和网络传输

## 枚举
```
enum sex{
male,female;
}
```
`sex male = sex.male;`

```for(sex s: sex.values()){
s.ordinal()  s.name()
}```


## 多线程的实现方式
1. 继承Thread类创建线程，重写run方法
2. 实现Runnable接口创建线程，实现run方法
3. 实现Callable接口创建Thread线程，重写call方法，有返回值
4. 使用ExecutorService、Callable、Future实现有返回结果的线程

## 线程优先级
setpriority()  
MAX_PRIORITY  MIN  NORM

## synchronized
synchronized(this){}同步代码块
保证数据的合法性，速度慢

同步方法：
`public synchronized void sale(){}`


## 死锁小案例


```
public class TestDemo implements Runnable { 
 static First first = new First() ; 
 static Second second = new Second() ; 
 public static void main(String[] args) { 
  new TestDemo() ; 
 } 
 @Override  
 public void run() { 
  first.fun(second); 
 } 
 public TestDemo() { 
  new Thread(this).start() ; 
  second.fun(first); 
 } 
} 
```


## stringbuffer与stringbuilder
string stringbuffer  extends charsequence 接口
stringbuffer同步  stringbuilder异步 

## 日期与字符串
SimpleDateFormate sdf  = new SimpleDateFormate("yyyy-MM-dd HH:mm:ss.SSS")
sdf.formate(date) ——> string
sdf.parse(str) ——> date

## util.date 和  sql.date
new Date(long)
date.getTime()  返回值为long
util.date 和  sql.date 转换 使用long


## 比较
 T  implements Comparable<T>    public int compareTo(T t)
class  implements Comparator<T>   public int compare(T t1,T t1)
Arrays.sort(对象数组,class)

## 大数四舍五入
BigDecimal result = big.divide(new 
BigDecimal(1), scale, BigDecimal.ROUND_HALF_UP); 


## 反射

```
class<?> cls = Class.forName("com.demo.Book")
cls.newInstance();

Constuctor<?> cons[] = cls.getConstructors();
Constructor<?> cons = cls.getConstructor(String.class, double.class); 

method [] met = cls.getMethods():
Method setMet = cls.getMethod("方法名", 
String.class) ; 
 
setmet.invoke("方法实例","方法参数具体");

Field[] field = cls.getDeclaredFields();
Field[] field = cls.getDeclaredField("成员变量名");
field.setAccessible(true);  
取消封装才能操作
field.set(obj, "Java开发"); 设置属性内容
field.get(obj)

"".equalsIgnoreCase(className)  不考虑大小写
```

## List
list想让 remove、contais方法正常操作，必须要覆写 Object 类中的 equals()方法。
vector jdk1.0 同步安全   ArrayList jdk1.2  异步
List  可以重复增加顺序为保存顺序   Set不能重复 
想排序就要像对象数组一样实现Comparable或Comparator


## Set
treeSet  （区分重复 要实现comparable ） 不允许空和不同数据类型
HashSet 异步  （区分重复要 hashCode() ）  equals()
hashSet 无序  
treeSet有序  
方式一：让实体类实现Comparable接口，并且实现这个接口中提供的compareTo排序方法
方式二：额外写一个排序类，该类实现Comparator接口，并且在compare中实现排序
List c = new ArrayList();
Colletions.sort(c,new CardComparator());


## Map
Map中key不能重复
两个常用的子类：HashMap jdk1.2开始 异步、  Hashtable jdk1.0开始  不能有空值

## Collection与Map
Collection 是保存单个数据的，而 Map 是保存一对数据的；  
Collection 保存数据的目的是为了输出，而 Map 保存数据的目的是为了查询使用


## hashcode 16进制

## instanceof()  类型比较


## Itrator 与 Enumeration
Itrator
hasNext() next()  hasprevious()  prevoius()

Enumeration
hasMoreElements()   nextElement()

## 遍历Set
1.

```
for (Entry<Integer, String> entry : map.entrySet()) {
System.out.println(entry.getKey()+" "+entry.getValue());
```
	 
2.

```
for (Integer key : map.keySet()) {
System.out.println(key+" "+map.get(key));}
```

3.

```
Set<Integer> set = map.keySet();
Iterator it = set.iterator();
while(it.hasNext()){
System.out.println(map.get(it.next()));
}
```		 
		 
4.

```
Set<Entry<Integer,String>> set1 = map.entrySet();
Iterator it1 = set1.iterator();
while(it1.hasNext()){
	System.out.println(((Entry<Integer, String>) it1.next()).getKey());
}
```


## File
File fiel = new File()  
fiel.exites()
file.delete()  file.createNewFile()
File.separator  File.pathSeparator
file.getParentFile()  
file.isDirectory()
file.length()   file.lastModified()

## 总结
1.File可以操作文件以及创建文件目录；
2.OutputStream与InputStream主要操作的字节数据，以文件拷贝为主；
3.如果程序输出数据使用PrintWriter（PrintStream），而输入数据使用Scanner（BufferedReader）；


## 流
输入流：将数据读入内存的
输出流：将数据从内存中导出的

static transient  修饰的属性不需要序列化
字节流：outputStream  inputStream
字符流：writer   reader
字节流通向字符流的桥梁:InputStreamReader  反之OutputStreamWriter
缓冲流  打印流  对象流  序列化流  数据流

流的关闭：输入流先低端源头逐步到高端关闭，输出流相反（一句话从源头关闭 ）   高端绑定低端


## Properties
Properties 继承于 Hashtable
a:b     键：值
a=b
```
Properties p = new Properties();
FileInputStream f = new FileInputStream("d:\\test1.txt");
p.load(f);
String s =(String) p.get("a");
```

## input与output  级别
input:
FileInputStream->InputStreamReader->BufferedReadr

output:
FileOutputStream->OutputStreamReader->BufferedWriter->PrintWriter


## 文件拷贝  输入  输出

```
File inFile= newFile("E:"+ File.separator+ "hello.jpg") ;// 输入路径
File outFile= newFile("E:"+ File.separator+ "my.jpg") ;// 输出路径
if(!outFile.getParentFile().exists()) {// 输出必须要考虑到目录问题
outFile.getParentFile().mkdirs() ;}
InputStream input = new FileInputStream(inFile) ;
OutputStream output = new FileOutputStream(outFile) ;
bytedata [] = new byte[2048] ;// 每次读取的数据量
int temp = 0 ;// 保存读取的长度
while((temp = input.read(data)) != -1) {// 读取的长度不是-1
output.write(data, 0, temp);}
output.close(); 
input.close();
```

```
PrintStream out= newPrintStream(newFileOutputStream(file)) ;
out.print("Hello World !");
out.close();
```

```
Scanner scan= newScanner(newFileInputStream(file)) ;
scan.useDelimiter("\n") ;
while(scan.hasNext()) {
System.out.println(scan.next());}
scan.close();
```

## ServerSocket  Socket  小例子

```
ServerSocket server = new ServerSocket(8888) ;   
System.out.print("等待客户端连接.........");   
Socket client = server.accept() ; // 接收客 户端连接 
   
PrintStream out = new PrintStream(client.getOutputStream()) ;  
out.println("Hello World ."); // 要输出的信息 
   
out.close() ; 
client.close();  
server.close(); 
```


```
Socket client = new Socket("localhost",8888) ; 
Scanner scan = new Scanner(client.getInputStream()) ; 
  scan.useDelimiter("\n") ; 
  if (scan.hasNext()) { 
   System.out.println(scan.next()); 
  } 
  scan.close(); 
  client.close(); 
```

## 常见的运行时异常
RuntimeException：NumberFormatException、ArithmeticException、 ClassCastException、NullPointerException 
ArrayIndexOutOfBoundsException 

## Exception  Error
所有的异常都属于 Throwable 的子类。
Error：指的是 JVM 出错，此时的程序还没有正常执行
Exception 异常的最大父类。Exception 是 RuntimeException 的父类；  
e.printStackTrace()


## throws与throw
throws 用于方法声明处，表示此方法不处理异常，而交给方法的被调用处进行处理； 
throw 用于方法之中，进行人为的异常对象抛出。


## 异常
RuntimeException 可以由用户 选择性的进行处理。 
自定义异常 写一个类继承 RuntimeException实现,有构造方法的实现，super(msg);  

非运行时异常，异常会强制要求调用者处理异常
运行时异常不会强制要求调用者处理异常，但是运行时会将异常自动抛给调用者


## 数据库语言
* DDL  数据定义语言Data Definition Language
* DML（Data Manipulation Language）数据操纵语言
* DCL（Data Control Language）数据库控制语言  授权，角色控制等
* TCL（Transaction Control Language）事务控制语言

## JDBC操作概要

```
Connection con = null;
PreparedStatement sta = null;
ResultSet result = null;
Class.forName("com.mysql.jdbc.Driver");
con = DriverManager.getConnection("jdbc:myaql://localhost:3306/base/","root","123");

sta = con.prepareStatement("select * from student where name=?");
sta.setString(1, "zhangsan");

result = sta.executeQuery();
while(result.next()){
System.out.println(result.getString("username"));
}

result.close();
sta.close();
con.close();
```


## 批处理和事务处理  
addBath();  executeBach()  conn.rollback
conn.commint  setSutoCommit()

## 查询本部门的平均工资  
使用自身关联

## 记忆点
* alter与alert  private不能修饰局部变量。
* avg(NVL(bonus,0))  oracle
* constraint c_fk foreign key(stu_id,course_id)
* references example2(stu_id,course_id)
* to_char(hire_date, 'yyyy') 
* case when then  else end