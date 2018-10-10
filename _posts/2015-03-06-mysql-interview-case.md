---
layout: post
title:  "Mysql 面试经典例子"
date:   2015-03-06 09:20:23
categories: MySQL
tags: MySQL Interview
excerpt: Mysql例子学习。
---

* content
{:toc}
# 转载

![mysql](http://oms5nhjmw.bkt.clouddn.com/mysql_interview.png)


1. 查询每个月倒数第 2 天入职的员工的信息. 
  
```
	select last_name, hire_date
	from employees
	where hire_date = last_day(hire_date) – 1
```
  
2. 查询出 last_name 为 'Chen' 的 manager 的信息.

  
	1). 通过两条 sql 查询:
  
```
select manager_id
from employees
where lower(last_name) = 'chen' --返回的结果为 108
```
  
```
select *
from employees
where employee_id = 108
```


  
	2). 通过一条 sql 查询(自连接):


  
```
select m.*
from employees e, employees m
where e.manager_id = m.employee_id and e.last_name = 'Chen'
```


  
	3). 通过一条 sql 查询(子查询):	


  
```
select *
from employees
where employee_id = (select manager_id 
		      from employees
		      where last_name = 'Chen')	
```



  
3. 查询平均工资高于 8000 的部门 id 和它的平均工资.
  

```
SELECT department_id, avg(salary)
FROM employees e 
GROUP BY department_id
HAVING avg(salary) > 8000
```
  

4. 查询工资最低的员工信息: last_name, salary
  
		```
		SELECT last_name, salary
		FROM employees
		WHERE salary = (
			SELECT min(salary)
			FROM employees
		)
```
  

5. 查询平均工资最低的部门信息
  
		```
		SELECT *
		FROM departments
		WHERE department_id = (
			SELECT department_id
			FROM employees
			GROUP BY department_id 
			HAVING avg(salary) = (
				SELECT min(avg(salary))
				FROM employees
				GROUP BY department_id
			) 
		)
		```
  
6. 查询平均工资最低的部门信息和该部门的平均工资
  

```
select d.*, (select avg(salary) from employees where department_id = d.department_id)
from departments d
where d.department_id = (
      SELECT department_id
      FROM employees
      GROUP BY department_id 
      HAVING avg(salary) = (
			 SELECT min(avg(salary))
			 FROM employees
			 GROUP BY department_id))
```
  

7. 查询平均工资最高的 job 信息
  
	1). 按 job_id 分组, 查询最高的平均工资	
  
	```
	SELECT max(avg(salary))
	FROM employees
	GROUP BY job_id
	```
	  
	2). 查询出平均工资等于 1) 的 job_id
  
	```
	SELECT job_id
	FROM employees
	GROUP BY job_id
	HAVING avg(salary) = (
		SELECT max(avg(salary))
		FROM employees
		GROUP BY job_id
	)
	```
  
	3). 查询出 2) 对应的 job 信息
  
	```
	SELECT *
	FROM jobs
	WHERE job_id = (
		SELECT job_id
		FROM employees
		GROUP BY job_id
		HAVING avg(salary) = (
			SELECT max(avg(salary))
			FROM employees
			GROUP BY job_id
		)
	)
	```
  
8. 查询平均工资高于公司平均工资的部门有哪些?
  
	1). 查询出公司的平均工资
  
	```
	SELECT avg(salary)
	FROM employees
	```
  
	2). 查询平均工资高于 1) 的部门 ID
  
	```
	SELECT department_id
	FROM employees
	GROUP BY department_id
	HAVING avg(salary) > (
		SELECT avg(salary)
		FROM employees
	)
	```
  
9. 查询出公司中所有 manager 的详细信息.
  
	1). 查询出所有的 manager_id
	  
	```
	SELECT distinct manager_id
	FROM employeess
	```
  
	2). 查询出 employee_id 为 1) 查询结果的那些员工的信息
  
	```
	SELECT employee_id, last_name
	FROM employees
	WHERE employee_id in (
		SELECT distinct manager_id
		FROM employees
	)
	```
  
	
10. 各个部门中 最高工资中最低的那个部门的 最低工资是多少
  
	1). 查询出各个部门的最高工资
	  
	```
	SELECT max(salary)
	FROM employees
	GROUP BY department_id
	```
	  
	2). 查询出 1) 对应的查询结果的最低值: 各个部门中最低的最高工资(无法查询对应的 department_id)
	  
	```
	SELECT min(max(salary))
	FROM employees
	GROUP BY department_id
	```
	
	3). 查询出 2) 所对应的部门 id 是多少: 各个部门中最高工资等于 2) 的那个部门的 id
	  
	```
	SELECT department_id
	FROM employees
	GROUP BY department_id 
	HAVING max(salary) = (
		SELECT min(max(salary))
		FROM employees
		GROUP BY department_id
	)
	```
	  
	4). 查询出 3) 所在部门的最低工资
	  
	```
	SELECT min(salary)
	FROM employees
	WHERE department_id = (
		SELECT department_id
		FROM employees
		GROUP BY department_id 
		HAVING max(salary) = (
			SELECT min(max(salary))
			FROM employees
			GROUP BY department_id
		)	
	)
```
  
11. 查询平均工资最高的部门的 manager 的详细信息: last_name, department_id, email, salary
	  
	1). 各个部门中, 查询平均工资最高的平均工资是多少
	  
	```
	SELECT max(avg(salary))
	FROM employees
	GROUP BY department_id
	```
	  
	2). 各个部门中, 平均工资等于 1) 的那个部门的部门号是多少
  
	```
	SELECT department_id
	FROM employees
	GROUP BY department_id
	HAVING avg(salary) = (
		SELECT max(avg(salary))
		FROM employees
		GROUP BY department_id
	)
	```
	  
	3). 查询出 2) 对应的部门的 manager_id
	  
	```
	SELECT manager_id
	FROM departments
	WHERE department_id = (
		SELECT department_id
		FROM employees
		GROUP BY department_id
		HAVING avg(salary) = (
			SELECT max(avg(salary))
			FROM employees
			GROUP BY department_id
		)	
	)
	```
	  
	4). 查询出 employee_id 为 3) 查询的 manager_id 的员工的 last_name, department_id, email, salary
	  
	```
	SELECT last_name, department_id, email, salary
	FROM employees
	WHERE employee_id = (
		SELECT manager_id
		FROM departments
		WHERE department_id = (
			SELECT department_id
			FROM employees
			GROUP BY department_id
			HAVING avg(salary) = (
				SELECT max(avg(salary))
				FROM employees
				GROUP BY department_id
			)	
		)	
	)
	```
	  
12. 查询 1999 年来公司的人所有员工的最高工资的那个员工的信息.
  
		1). 查询出 1999 年来公司的所有的员工的 salary
		  
		```
		SELECT salary
		FROM employees
		WHERE to_char(hire_date, 'yyyy') = '1999'
		```
		  
		2). 查询出 1) 对应的结果的最大值
		  
		```
		SELECT max(salary)
		FROM employees
		WHERE to_char(hire_date, 'yyyy') = '1999'
		```
		  
		3). 查询工资等于 2) 对应的结果且 1999 年入职的员工信息	
		  
		```
		SELECT *
		FROM employees
		WHERE to_char(hire_date, 'yyyy') = '1999' AND salary = (
			SELECT max(salary)
			FROM employees
			WHERE to_char(hire_date, 'yyyy') = '1999'
		)
		```
		  
13. 返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工的员工号、姓名、job_id 以及salary
  
```
SELECT employee_id, last_name, job_id, salary
FROM   employees
WHERE  salary < ALL
                    (SELECT salary
                     FROM   employees
                     WHERE  job_id = 'IT_PROG')
AND    job_id <> 'IT_PROG';
```