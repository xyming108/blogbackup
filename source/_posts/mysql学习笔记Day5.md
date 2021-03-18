---
title: mysql学习笔记Day5
date: 2021-03-18 09:19:23
tags:
- mysql
- 数据库
categoreies: 数据库
description: mysql数据库的流程控制、函数
top_img:
cover: https://gitee.com/xyming108/blog_img/raw/master/wallpaper/b1.jpg
---

[TOC]



# Day



### **1、函数**

```mysql
/*
含义：一组预先编译好的SQL语句的集合，可理解成批处理语句
	1、提高代码的重用性
	2、简化操作
	3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率
	
区别：
	存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新
	函数：有且仅有一个返回，适合做处理数据后返回一个结果
*/

/*
一、创建语法
create function 函数名（参数列表） return 返回类型
begin
	函数体
end

注意：
	1、参数列表包含两部分：
		参数名、参数类型
	2、函数体：肯定会有return语句，如果没有会报错，如果return语句没有放在函数体的最后不会报错，但不建议return 值
	3、函数体中仅有一句话，则可以省略begin end
	4、使用 delimiter 语句设置结束标记

二、调用语法
select 函数名（参数列表）

三、查看函数
show create function 函数名;

四、删除函数
drop function 函数名;
*/

#返回公司的员工个数（无参返回）
create function myf1() returns int
begin
	declare c int default 0	#定义变量
	select count(*) into c	#赋值
	from employees;
	return c;
end $

#根据员工名返回他的工资（有参返回）
create function myf2(cmpName varchar(20)) return double
begin
	set @sal=0;
	select salary into @sal
	from employees
	where last_name = empName;
	return @sal;
end $
```

### **2、流程控制图**

```mysql
/*
顺序结构：程序从上往下依次执行
分支结构：程序从两条或多条路径中选择一条去执行
循环结构：程序在满足一定条件的基础上，重复执行一段代码
*/
一、分支结构
1、 if 函数
	语法：if(条件，值1，值2)
	特点：可以用在任何位置
2、 case 结构
	情况一：类似于switch
    case 表达式
    when 值1 then 结果1或语句1(如果是语句，需要加分号) 
    when 值2 then 结果2或语句2(如果是语句，需要加分号)
    ...
    else 结果n或语句n(如果是语句，需要加分号)
    end 【case】（如果是放在begin end中需要加上case，如果放在select后面不需要）

    情况二：类似于多重if
    case 
    when 条件1 then 结果1或语句1(如果是语句，需要加分号) 
    when 条件2 then 结果2或语句2(如果是语句，需要加分号)
    ...
    else 结果n或语句n(如果是语句，需要加分号)
    end 【case】（如果是放在begin end中需要加上case，如果放在select后面不需要）
3、 if 结构
    if 情况1 then 语句1;
    elseif 情况2 then 语句2;
    ...
    else 语句n;
    end if;
    
二、循环结构
语法：
	【标签：】WHILE 循环条件  DO
        循环体
    END WHILE 【标签】;
    
特点：
    只能放在BEGIN END里面
    如果要搭配leave跳转语句，需要使用标签，否则可以不用标签
    leave类似于java中的break语句，跳出所在循环！！！
```

<img src="C:%5CUsers%5CHasee%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210112202633843.png" alt="image-20210112202633843" style="zoom:80%;" />