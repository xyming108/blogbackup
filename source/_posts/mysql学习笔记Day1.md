---
title: mysql学习笔记Day1
date: 2021-03-18 09:10:47
tags:
- 数据库
- mysql
categoreies: 数据库
description: mysql数据库的基本概念，基本查询语句
top_img:
cover: https://gitee.com/xyming108/blog_img/raw/master/wallpaper/b8.jpg
---

[TOC]

# Day1

### 1、**数据库的相关概念**

一、数据库的好处

```
1.持久化数据到本地
2.可以实现结构化查询，方便管理
```

二、常见概念

```
1、DB：数据库，保存一组有组织的数据的容器
2、DBMS：数据库管理系统，又称为数据库软件（产品），用于管理DB中的数据
3、SQL: 结构化查询语言，用于和DBMS通信的语言
```

三、数据库存储数据的特点

```
1、将数据放到表中，表再放到库中
2、一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己。表名具有唯一性。
3、表具有一些特性，这些特性定义了数据在表中如何存储，类似java中“类”的设计。
4、表由列组成，我们也称为字段。所有表都是由一个或多个列组成的，每一列类似java 中的”属性”
5、表中的数据是按行存储的，每一行类似于java中的“对象”
```

### 2、**MySQL的简单使用**

一、MySQL的启动和停止

```
启动：net start 服务名（例如：net start mysql80）

停止：net stop 服务名（例如：net stop mysql80）
```

二、MySQL的登录和退出

```
登录：mysql -h 主机名 -P 端口号 -u 用户名 -p密码 
	（注意：-p和密码中间不能加空格）
	（例如：mysql 【-h localhost P 3306】 -u root -p564445 ，若是登录本地的，【】内的可以不写）

退出：exit 或 ctl+c
```

### 3、**MySQL常见命令**

```
show databases； 查看所有的数据库
use 库名； 打开指定 的库
show tables ; 显示库中的所有表
show tables from 库名;显示指定库中的所有表
create table 表名(
	字段名 字段类型,	
	字段名 字段类型
); 创建表

desc 表名; 查看指定表的结构
select * from 表名;显示表中的所有数据
```

### 4、**基础、条件查询**

```mysql
select 字段名 from 表名;
select 字段名1，字段名2，...... from 表名;
select * from 表名;
select 常量值;（注意：字符型和日期型的常量值要用单引号括起来，数值型不需要）
select 函数名;
select 表达式;
select 数值+数值; 	/*结果为数值*/
select 字符+数值;	/*试图将字符转换成数值，转换成功则继续运算，转换不成功则把字符当成成0再运算*/
select null+值;	/*结果都为null*/

#别名
select 字段名 as 别名 from 表名;
select 字段名 别名 from 表名;	/*可直接用空格代替as*/

#去重
select distinct 字段名 from 表名;

#concat函数
select concat（字符1，字符2，......） from 表名;

#ifnull函数
#判断某字段或表达式是否为null，如果为null，返回指定的值（比如指定0），否则返回原本的值
select ifnull(字符, 0) from 表名;

#isnull
#判断某字段或表达式是否为null，是则返回1，不是则返回0
select 字符1 from 表名 where 字符1 is null;
select 字符1 from 表名 where 字符1 is not null;
```

#### 条件运算符

	>
	<
	>= 
	<= 
	= ，<=> 安全等于
	!= ，<>

#### 逻辑运算符

	and（&&）:两个条件如果同时成立，结果为true，否则为false
	or(||)：两个条件只要有一个成立，结果为true，否则为false
	not(!)：如果条件成立，则not后为false，否则为true

#### 模糊查询

	like 
	between and
	in
	is null

#### 实践代码

```mysql
use myemployees;

select * from employees;
select first_name from employees; 
select first_name as hhh from employees; 
select first_name hhh from employees; 
select distinct department_id from employees; 
select ifnull(commission_pct, 0)  as 奖金率, commission_pct from employees;
select concat(first_name,'___' ,last_name,'___',ifnull(commission_pct, 0)) as output from employees; 
select 100;
select 'xym';
select 100*20 as resulet;
select version();

#------------------------------------------------------------------------------------------------------------------------------------
/*
select 
		查询列表
from
		表名
where
		筛选条件;
*/

select * from employees where salary>12000;
select last_name, department_id from employees where department_id <> 90;

select last_name, salary, commission_pct 
from employees 
where salary >= 10000 and salary <= 20000;

select *
from employees
where not(department_id < 90 or department_id>110) 
	  or salary > 15000;

/*通配符：%任意多个字符
		  _任意单个字符
*/
select * from employees where last_name like '%a%';
select * from employees where last_name like '_e_l';

select * 
from employees 
where last_name 
like '_$_%' escape '$';*/  /*查询第二个字符为_的员工，转义*/

select * 
from employees 
where employee_id 
between 100 and 120;  /*包含临界值（闭区间），不能颠倒大小*/

select last_name, job_id
from employees 
#where job_id='IT_PROT' or  job_id= 'AD_VP' or job_id = 'AD_PRES';
where job_id in('IT_PROT','AD_VP','AD_PRES')

/*
is null:仅可以判断 null
<=>:既可以判断 null，又可以判断数值
*/

select last_name, commission_pct
from employees 
#where commission_pct is null;
where commission_pct is not null;

select last_name, commission_pct
from employees 
where commission_pct <=> null;   /*安全等于*/

select last_name, commission_pct, salary
from employees 
where salary <=> 12000;   /*安全等于*/
```