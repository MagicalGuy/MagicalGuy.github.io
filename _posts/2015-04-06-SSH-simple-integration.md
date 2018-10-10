---
layout: post
title:  "Struts,Spring,Hibernate简单整合"
date:   2015-04-06 20:12:06
categories: Struts Spring Hibernate SSH
tags: Struts Spring Hibernate SSH
---

* content
{:toc}

## SSH的简单整合

>本文通过ssh三大框架实现一个小demo,通过查询在jsp页面显示员工信息

### 整合步骤：（Eclipse  新建javaweb工程）
	1. 引入SSH Jar文件
		Struts 核心jar
		Hibernate 核心jar
		Spring
			Core  核心功能
			Web  对web模块支持
			Aop   aop支持
			Orm   对hibernate支持
			Jdbc/tx  jdbc支持包、事务相关包

	2. 配置
		Web.xml   初始化struts功能、spring容器
		Struts.xml   配置请求路径与映射action的关系
		Spring.xml  IOC容器配置
			bean-base.xml     【公用信息】
			bean-service.xml
			bean-dao.xml
			bean-action.xml

	3. 开发
		Entity/Dao/service/action

---

* 部门实体类

```
package cn.zhongkai.entity;

public class Dept {

	private int id;
	private String name;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```

* 部门类对应映射文件Dept.hbm.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE hibernate-mapping PUBLIC 
	"-//Hibernate/Hibernate Mapping DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="cn.zhongkai.entity">

	<class name="Dept" table="t_dept">
		<id name="id" column="deptId">
			<generator class="native"></generator>
		</id>
		<property name="name" column="deptName"></property>
	</class>

</hibernate-mapping>
```

***

* 员工实体类

```
package cn.zhongkai.entity;

public class Employee {

	private int id;
	private String empName;
	private double salary;
	private Dept dept;
	
	
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getEmpName() {
		return empName;
	}
	public void setEmpName(String empName) {
		this.empName = empName;
	}
	public double getSalary() {
		return salary;
	}
	public void setSalary(double salary) {
		this.salary = salary;
	}
	public Dept getDept() {
		return dept;
	}
	public void setDept(Dept dept) {
		this.dept = dept;
	}
	
	
}

```

* 员工类对应映射文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE hibernate-mapping PUBLIC 
	"-//Hibernate/Hibernate Mapping DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="cn.zhongkai.entity">

	<class name="Employee" table="t_employee">
		<id name="id" column="empId">
			<generator class="native"></generator>
		</id>
		<property name="empName"></property>
		<property name="salary"></property>
		<!-- 多对一 -->
		<many-to-one name="dept" column="dept_id" class="Dept"></many-to-one>
	</class>

</hibernate-mapping>

```
***

* dao层

```
package cn.zhongkai.dao;

import java.io.Serializable;

import org.hibernate.SessionFactory;

import cn.zhongkai.entity.Employee;

public class EmployeeDao {

	// 注入SessionFactory对象
	private SessionFactory sessionFactory;
	public void setSessionFactory(SessionFactory sessionFactory) {
		this.sessionFactory = sessionFactory;
	}
	
	/**
	 * 查询
	 * @param emp
	 */
	public Employee findById(Serializable id) {
		return (Employee) sessionFactory.getCurrentSession().get(Employee.class, id);
	}
}
```

***

* service层

```
package cn.zhongkai.service;

import java.io.Serializable;

import org.hibernate.SessionFactory;

import cn.zhongkai.dao.EmployeeDao;
import cn.zhongkai.entity.Employee;

public class EmployeeService {
	
	// IOC注入
	private EmployeeDao employeeDao;
	public void setEmployeeDao(EmployeeDao employeeDao) {
		this.employeeDao = employeeDao;
	}

	/**
	 * 查询
	 * @param emp
	 */
	public Employee findById(Serializable id) {
		Employee emp = employeeDao.findById(id);
		return emp;
	}
}

```
***

* action层

```
package cn.zhongkai.action;

import java.util.Map;

import cn.zhongkai.entity.Employee;
import cn.zhongkai.service.EmployeeService;

import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;


public class EmployeeAction extends ActionSupport{
	
	// IOC容器注入
	private EmployeeService employeeService;
	public void setEmployeeService(EmployeeService employeeService) {
		this.employeeService = employeeService;
	}
	
	@Override
	public String execute(){
		int empid = 11;
		// 调用Service
		Employee emp = employeeService.findById(empid);
		// 保存到request
		Map<String,Object> request = (Map<String, Object>) ActionContext.getContext().get("request");
		request.put("emp", emp);
		
		return SUCCESS;
	}
}
```
***


* 配置web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
	http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
	
	<!-- 配置spring的OpenSessionInView模式 【目的：JSp页面访问懒加载数据】 -->
	<!-- 注意：访问struts时候需要带上*.action后缀 -->
	<filter>
		<filter-name>OpenSessionInView</filter-name>
		<filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>OpenSessionInView</filter-name>
		<url-pattern>*.action</url-pattern>
	</filter-mapping>

	<!-- struts2配置 -->
	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

	<!-- Spring配置 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:bean*.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>
</web-app>
```

***

* struts配置文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>

	<package name="emp" extends="struts-default">

		<!-- action实例交给spring容器创建 -->
		<action name="show" class="employeeAction" method="execute">
			<result name="success">/index.jsp</result>
		</action>

	</package>

</struts>
```

***

*index.jsp

```
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    
    <title>My JSP 'index.jsp' starting page</title>
	<meta http-equiv="pragma" content="no-cache">
	<meta http-equiv="cache-control" content="no-cache">
	<meta http-equiv="expires" content="0">    
	<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
	<meta http-equiv="description" content="This is my page">
	<!--
	<link rel="stylesheet" type="text/css" href="styles.css">
	-->
  </head>
  
  <body>
  	员工： ${emp.empName }
  	部门： ${emp.dept.name }
  </body>
</html>
```


***

* pring配置文件

### bean-base.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
    	 http://www.springframework.org/schema/beans/spring-beans.xsd
     	 http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
         http://www.springframework.org/schema/tx
     	 http://www.springframework.org/schema/tx/spring-tx.xsd">

	
	<!-- 所有配置的公共部门 -->
	
	<!-- 1) 连接池实例 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql:///hib_demo"></property>
		<property name="user" value="root"></property>
		<property name="password" value="root"></property>
		<property name="initialPoolSize" value="3"></property>
		<property name="maxPoolSize" value="6"></property>
	</bean>

	<!-- 2) SessionFactory实例创建 -->
	<!-- 所有的配置都由spring维护(项目中不需要hibernate.cfg.xml啦) -->
	<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
		<!-- a. 连接池 -->
		<property name="dataSource" ref="dataSource"></property>
		
		<!-- b. hibernate常用配置： 方言、显示sql、自动建表等 -->
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
				<prop key="hibernate.show_sql">true</prop>
				<prop key="hibernate.hbm2ddl.auto">update</prop>
			</props>
		</property>
		
		<!-- c. 映射配置 -->
		<property name="mappingLocations">
			<list>
				<value>classpath:cn/zhongkai/entity/*.hbm.xml</value>
			</list>
		</property>
	</bean>
	
	<!-- 3) 事务配置 -->
	<!-- # 事务管理器 -->
	<bean id="txManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory"></property>
	</bean>
	<!-- # 事务增强 -->
	<tx:advice id="txAdvice" transaction-manager="txManager">
		<tx:attributes>
			<tx:method name="*" read-only="false"/>
		</tx:attributes>
	</tx:advice>
	<!-- # AOP配置 -->
	<aop:config>
		<aop:pointcut expression="execution(* cn.zhongkai.service.*.*(..))" id="pt"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="pt"/>
	</aop:config>
	
</beans>     
```


### bean-dao.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
    	 http://www.springframework.org/schema/beans/spring-beans.xsd
     	 http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
         http://www.springframework.org/schema/tx
     	 http://www.springframework.org/schema/tx/spring-tx.xsd">

	
	<bean id="employeeDao" class="cn.zhongkai.dao.EmployeeDao">
		<property name="sessionFactory" ref="sessionFactory"></property>
	</bean>
	
</beans> 
```


### bean-service.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
    	 http://www.springframework.org/schema/beans/spring-beans.xsd
     	 http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
         http://www.springframework.org/schema/tx
     	 http://www.springframework.org/schema/tx/spring-tx.xsd">

	
	<bean id="employeeService" class="cn.zhongkai.service.EmployeeService">
		<property name="employeeDao" ref="employeeDao"></property>
	</bean>
	
</beans> 
```


### bean-action.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
    	 http://www.springframework.org/schema/beans/spring-beans.xsd
     	 http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
         http://www.springframework.org/schema/tx
     	 http://www.springframework.org/schema/tx/spring-tx.xsd">

	<bean id="employeeAction" class="cn.zhongkai.action.EmployeeAction" scope="prototype">
		<property name="employeeService" ref="employeeService"></property>
	</bean>
	
</beans> 
```
