---
layout: post
title:  "jquery结合ajax"
date:   2016-03-20 16:23:12
categories: JQuery AJAX JSON JSONP
tags: JQuery AJAX JSON JSONP
---

* content
{:toc}

# 转载

## JQuery对AJAX的支持
>Ajax原生的操作里面所有的代码几乎都是一直在进行重复，所有的Ajax的操作只有操作的路径、传输的参数、返回数据类型、回调函数的处理上。

1. 观察ajax()原生的函数操作支持；
2. 使用post与get提交进行数据的交换（text、xml、json）；
3. jsonp的数据传输问题。


### ajax原生支持函数

在jQuery里面提供有一个“$.ajax()”函数，这个函数里面可以设置各种参数，并且设置的类型以json的形式为主。
正因为json不仅可以定义文本数据，也正是因为其可以定义函数，所以在出现了错误或者操作成功之后可以直接利用json定义的函数进行处理。

** 范例：实现一个echo程序的功能**
>首先在servlet页面之中那么必须接收参数，而后进行回应：

```
package cn.mldn.servlet;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.IOException;@WebServlet(urlPatterns = "/EchoServlet")
public class EchoServlet extends HttpServlet {
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
// 为了能够测试异步发送的数据，现在进行参数的接收
	request.setCharacterEncoding("UTF-8");
	response.setCharacterEncoding("UTF-8");
	response.setContentType("text/html");
	response.setContentType("text/html");
	System.out.println("【参数msg = 】" + request.getParameter("msg"));
	System.out.println("【参数eid = 】" + request.getParameter("eid"));
	response.getWriter().print("ECHO : " + request.getParameter("msg"));
}
@Override
public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	this.doGet(request, response);
}
}
```

>定义前台页面，而后实现数据的异步发送；

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
<title>jQuery开发详解</title>
<script type="text/javascript" src="js/jquery-1.11.3.min.js"></script>

<script type="text/javascript">
	$(function() {
		$("#sendBut").on("click",function() {
			var msgValue = $("#msg").val() ;    // 得到内容
			$.ajax({
				url : "EchoServlet",    // ajax提交的路径
				data : {                // 要传输的数据
					"msg" : msgValue ,"eid" : 100}  ,type : "post" ,     // 表单的提交模式
					dataType : "text" ,      // 服务器返回的数据类型
					success : function(data) { // 操作完成之后的返回数据
					// 数据的类型由“dataType”属性来决定。
						$("#contentDiv").append("<p>" + data + "</p>") ;
				} ,
				error : function() {    // 异常处理alert("对不起，操作出现了异常，无法连接！") ;
			}
		}) ;  // 这个函数直接进行ajax的控制}) ;
	}) ;
</script>

</head>
<body>

<div>请输入内容：<input type="text" name="msg" id="msg">
	<input type="button" value="发送" id="sendBut">
</div>
<div id="contentDiv"></div>

</body>
</html>
```

>现在完成了文本数据的发送，而在实际的情况下可能不会只发送文本数据，最常见的两种情况就是：xml与json数据。

* 范例：修改EchoServlet程序，让其发送XML数据

```
@Overridepublic void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
// 为了能够测试异步发送的数据，现在进行参数的接收
	request.setCharacterEncoding("UTF-8");
	response.setCharacterEncoding("UTF-8");
	response.setContentType("text/xml");
	response.getWriter().print("<?xml version=\"1.0\"encoding=\"utf-8\"?>");
	response.getWriter().print("<members>");
	response.getWriter().print("<member>");
	response.getWriter().print("<name>张三</name>");
	response.getWriter().print("<age>20</age>");
	response.getWriter().print("</member>");
	response.getWriter().print("</members>");
}
```

此时只是生成了一个固定的xml文件数据，那么随后要做的事情就是进行xml数据的解析，但是由于此时返回的是xml数据，
那么需要考虑将ajax()函数定义的数据类型更换为xml。

** 范例：利用xml返回数据**

```
<script type="text/javascript">
	$(function() {$("#sendBut").on("click",function() {
		var msgValue = $("#msg").val() ;    // 得到内容
		$.ajax({url : "EchoServlet",    // ajax提交的路径
		data : {                // 要传输的数据
			"msg" : msgValue ,"eid" : 100}  ,type : "post" ,     // 表单的提交模式
			dataType : "xml",      // 服务器返回的数据类型
			success : function(data) { // 操作完成之后的返回数据// 数据的类型由“dataType”属性来决定。
				var name = data.getElementsByTagName("name") ;
				var age = data.getElementsByTagName("age") ;
				$("#contentDiv").append("<p>姓名：" + name[0].firstChild.nodeValue + "，年龄："
				+ age[0].firstChild.nodeValue + "</p>");
				} ,
			error : function() {    // 异常处理alert("对不起，操作出现了异常，无法连接！") ;}}) ;  
			// 这个函数直接进行ajax的控制}) ;
		}) ;
</script>

```

>在本程序之中可以发现“dataType”属性决定了返回值的数据类型，同时也可以找到对比：

* ajax传统：xmlHttpRequest.responseText（dataType : "text"）；
* ajax传统：xmlHttpRequest.responseXML（dataType : "xml"）；
* ajax传统：eval("(" + xmlHttpRequest.responseText + ")")（dataType : "json"）；

>任何情况下如果返回的数据是XML结构，这样的操作一定是不可取的，因为如果要想从xml里面取出数据实在是太麻烦了，
>所以现在的开发都是基于json完成的，那么也就是说此时返回json数据才是最为稳妥的做法。

** 范例：返回json数据 **


```
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
// 为了能够测试异步发送的数据，现在进行参数的接收
	request.setCharacterEncoding("UTF-8");response.setCharacterEncoding("UTF-8");
	response.setContentType("text/html");
	response.getWriter().print("{\"members\":[{\"name\":\"张三\",\"age\":\"20\"}]}");
}
```


** 范例：随后修改返回的数据类型，以及成功后的回调处理操作 **

```
<script type="text/javascript">
$(function() {
	$("#sendBut").on("click",function() {
	var msgValue = $("#msg").val() ;    // 得到内容
	$.ajax({url : "EchoServlet",    // ajax提交的路径
		data : {                // 要传输的数据
		"msg" : msgValue ,"eid" : 100}  ,
		type : "post" ,     // 表单的提交模式
		dataType : "json" ,      // 服务器返回的数据类型
		success : function(data) { // 操作完成之后的返回数据
		$("#contentDiv").append("<p>姓名：" + data.members[0].name + "，年龄：" + data.members[0].age + "</p>");} ,
		error : function(e) {    // 异常处理
			alert("对不起，操作出现了异常，无法连接！" + e) ;}}) ;  // 这个函数直接进行ajax的控制
	}) ;
}) ;
</script>
```


>如果服务器端返回的是json数据，接收的简单、数据的传输量也小、同时操作更简单。
>此时给出的$.ajax()处理函数是整个jQuery最为基础的ajax核心结构，后面会有更简化的结构，
>但是在许多的开发团队里面，为了更加严格的进行处理，往往会采用此类结构完成。


### get与post处理函数

虽然$.ajax()函数是异步操作的基础函数，但是这个函数里面需要传送大量的json结构数据。
在jQuery里面考虑到大部分的开发者可能只需要处理简单的异步处理，例如：只是进行简单的post或者是get处理，
所以专门提供了两个简化操作的函数：
* 处理post请求：$.post(地址,{参数},回调函数,返回值类型)；
* 处理get请求：$.get(地址,{参数},回调函数,返回值类型)；

** 范例：简化处理操作 **

```
<script type="text/javascript">
	$(function () {
		$("#sendBut").on("click", function () {
			var msgValue = $("#msg").val();    // 得到内容
			$.post("EchoServlet", {"msg": msgValue, "eid": 100}, 
			function (data) {
				$("#contentDiv").append("<p>姓名：" + data.members[0].name + "，年龄：" + data.members[0].age + "</p>");
			}, "json");
		});
	});
</script>

```

>现在的开发你不需要对失败进行控制，并且需要简化操作的时候，以上形式的函数要比直接使用ajax()更加的容易。
>如果要想修改返回值类型，那么可以修改的类型也包含有如下几种：

* 文本类型：
```
$.post(
	"EchoServlet", 
	{"msg": msgValue, "eid": 100}, 
	function (data) {
		$("#contentDiv").append("<p>姓名：" + data.members[0].name + "，年龄：" + data.members[0].age + "</p>");
		}, 
	"text"
);
```

* XML类型：

```
$.post(
	"EchoServlet", 
	{"msg": msgValue, "eid": 100}, 
	function (data) {
		$("#contentDiv").append("<p>姓名：" + data.members[0].name + "，年龄：" + data.members[0].age + "</p>");},
	"xml"
);

```
>如果开发要求不严格，这样的两个函数绝对可以胜任所有的ajax开发，而且更加的简单。


### JSONP实现跨域数据交换

JSONP实现跨域数据交换JSON Vs JSONP？
所谓的JSON实际上是一种数据的传输格式，而JSONP（JSON  With Padding）指的是针对于JSON数据的一种处理形式，跨域操作。
例如，现在有两个网址：
* 第一个：www.mldn.cn/EchoServlet，返回JSON的数据；
* 第二个：www.pp.com/demo.htm，接收JSON数据。


![jsonp](http://oms5nhjmw.bkt.clouddn.com/jsonp.png)


如果要想实现跨域的模拟操作，那么必须虚拟出两个域名。

* 路径：C:\Windows\System32\drivers\etc\hosts打开此文件之后建立两个域名：
`127.0.0.1www.mldn.cn`
`127.0.0.1www.pp.com`

两个域名定义完成并且保存之后就可以通过浏览器直接进行访问了。随后需要修改ajax请求的访问路径。

```
<script type="text/javascript">
	$(function() {
		$("#sendBut").on("click",function() {
			var msgValue = $("#msg").val() ;    // 得到内容
			$.ajax(
			{url : "http://www.pp.com/EchoServlet",    // ajax提交的路径
			data : {                // 要传输的数据
			"msg" : msgValue ,
			"eid" : 100}  ,
			type : "post" ,     // 表单的提交模式
			dataType : "json" ,      // 服务器返回的数据类型
			success : function(data) { // 操作完成之后的返回数据
			$("#contentDiv").append("<p>姓名：" + data.members[0].name + "，年龄：" + data.members[0].age + "</p>");} ,
			error : function(e) {    // 异常处理
			alert("对不起，操作出现了异常，无法连接！" + e) ;}
			}) ;  // 这个函数直接进行ajax的控制
		}) ;
	}) ;
</script>
```


>如果ajax_demo.htm页面运行在www.mldn.cn上，那么在访问EchoServlet的时候就进行了跨域的访问操作。
>此时由于是跨域访问，所以页面执行之后出现了如下的错误提示信息。

```
ajax_demo.jsp:1      XMLHttpRequest      cannot      load      http://www.pp.com/EchoServlet.    
No 'Access-Control-Allow-Origin'    header    is    present    on    the    requested    resource.  
Origin 'http://www.mldn.cn' is therefore not allowed access.
```

>如果说进行跨域操作的话，此时的异步加载根本就无法起任何的作用。那么该怎么解决这样的问题呢？
>这个解决的方式就是依靠jsonp的概念，不过遗憾的是，这一操作之中需要注意多传一个参数。
>解决第一步：当进行跨域操作的时候访问的路径是EchoServlet，客户端一定有可能向服务器端传递信息，
>可是在使用jsonp的过程里面需要接收一个“jsonpcallback”的参数。


```
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
// 为了能够测试异步发送的数据，现在进行参数的接收
	request.setCharacterEncoding("UTF-8");
	response.setCharacterEncoding("UTF-8");
	response.setContentType("text/html");
	System.out.println("【请求参数】msg = " + request.getParameter("msg"));
	System.out.println("【请求参数】eid = " + request.getParameter("eid"));
	String jsonpcallback = request.getParameter("jsonpcallback") ;
	String content = "{\"members\":[{\"name\":\"张三\",\"age\":\"20\"}]}";
	response.getWriter().print(jsonpcallback + "(" + content + ")");
}
```

>解决第二步：修改前台调用的操作

```
<script type="text/javascript">
	$(function() {
		$("#sendBut").on("click",function() {
		var msgValue = $("#msg").val() ;    // 得到内容
		$.ajax({url : "http://www.pp.com/EchoServlet",    // ajax提交的路径
			data : {                // 要传输的数据
			"msg" : msgValue ,"eid" : 100}  ,
			type : "post" ,     // 表单的提交模式
			dataType : "jsonp" ,      // 服务器返回的数据类型
			jsonp : "jsonpcallback" ,
			success : function(data) { // 操作完成之后的返回数据
			$("#contentDiv").append("<p>姓名：" + data.members[0].name + "，
			年龄：" + data.members[0].age + "</p>");} ,
			error : function(e) {    // 异常处理
			alert("对不起，操作出现了异常，无法连接！" + e) ;
			}}) ;  // 这个函数直接进行ajax的控制
		}) ;
	}) ;
</script>
```

>此时就完成了跨域的数据访问。发现此时$.ajax()函数里面编写的代码还是多，有没有更简化一点的呢？
>可以使用“$.getJSON()”函数完成。

** 范例：利用“$.getJSON()”实现json的数据取得 **

```
<script type="text/javascript">
	$(function() {$("#sendBut").on("click",function() {
		var msgValue = $("#msg").val() ;    // 得到内容
		$.getJSON("http://www.pp.com/EchoServlet?jsonpcallback=?",
		{"msg":"hello","eid":"100"},
		function(data){
			$("#contentDiv").append("<p>姓名：" +data.members[0].name + "，年龄：" 
			+ data.members[0].age + "</p>");},
		"json") ;
		}) ;
	}) ;
</script>
```

### 总结

1. 如果要是简单就使用$.get()、$.post()；
2. 如果要标准就使用$.ajax()。
