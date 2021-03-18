---
title: mysql学习笔记Day2
date: 2021-03-18 09:13:47
tags:
- mysql
- 数据库
categoreies: 数据库
description: mysql数据库常用函数，排序查询
top_img:
cover: https://gitee.com/xyming108/blog_img/raw/master/wallpaper/b2.jpg
---

[TOC]

# Day2

### **1、排序查询**

```mysql
#语法
select
	要查询的东西
from
	表
where 
	条件

order by 排序的字段|表达式|函数|别名 【asc|desc】
(默认升序)
```

#### 实践代码

```mysql
select * from employees order by salary desc;
select * from employees order by salary asc;

select * 
from employees
where department_id >= 90
order by hiredate asc;

#按年薪的高低显示员工的信息和年薪（按表达式排序）
select *, salary*12*(1+ifnull(commission_pct, 0)) as 年薪
from employees
#order by salary*12*(1+ifnull(commission_pct, 0)) desc;
order by 年薪 desc;

#按姓名长度显示员工的姓名和工资【按函数排序】
select length(last_name) as 字节长度, last_name, salary
from employees
order by 字节长度 desc;

#查询员工信息，先按工资什序，再按员工编号降序【按多个字段排序】
select *
from employees
order by salary asc, employee_id desc;
```

### **2、常见函数**

```mysql
#语法
select 函数名()
【from 表】
```

#### **单行函数**

##### 字符函数

```mysql
/*concat拼接
substr截取子串
upper转换成大写
lower转换成小写
trim去前后指定的空格和字符
ltrim去左边空格
rtrim去右边空格
replace替换
lpad左填充
rpad右填充
instr返回子串第一次出现的索引
length 获取字节个数
*/

select length('length');
select upper('select');
select lower('WERTY');
select concat(upper(last_name), lower(first_name)) 姓名 from employees;
select substr('李莫愁神雕侠侣', 4) as aa;	#索引从1开始
select substr('李莫愁神雕侠侣', 4, 2) as aa;	#后面的2表示字符长度
select concat(substr(last_name, 1, 1), '_', lower(substr(last_name, 2))) aa from employees;
select instr('倚天屠龙记屠龙记', '屠龙记') as aa;  #找不到返回0
select length(trim('     子怡     ')) as aa;
select trim('a' from 'aaa子怡aaaa') as aa;
select lpad('你好', 10, '*') output;
select rpad('你好', 10, '*') output;
select replace('张无忌_周芷若_周芷若_周芷若', '周芷若', '赵敏') aa;
```

##### 数学函数

```mysql
/*round 四舍五入
rand 随机数
floor向下取整
ceil向上取整
mod取余
truncate截断*/


select round(-1.35);
select round(1.5664, 2);
select ceil(1.564);		#2
select ceil(1);		#1
select floor(1.564);	#1
select floor(1);	#1
select truncate(1.6932, 2);
select mod(10, 3);		#1
select mod(-10, 3);		#-1
select mod(10, -3);  	#1
select mod(-10, -3);	#-1  总结：符号和被除数保持一致
```



##### 日期函数

```mysql
/*now当前系统日期+时间
curdate当前系统日期
curtime当前系统时间
datediff(date1, date2)日期之差date1-date2
str_to_date 将字符转换成日期
date_format将日期转换成字符
*/

select now();
select curdate();
select curtime();
select year(now()) 年, month(now()) 月;
select year('1998-8-18') 年;
select monthname(now()) 月;
select datediff(now(), '1998-10-8');

#str_to_data：将日期格式的字符转换成指定格式日期
select str_to_date('1992-4-3', '%Y-%m-%d') output;
#查询入职日期为1992-4-3的员工信息
select * from employees where hiredate='1992-4-3';
select * from employees where hiredate=str_to_date('4-3-1992', '%m-%d-%Y');

#date-format：将日期转换成字符
select date_format(now(), '%Y年%m月%d日');
#查询有奖金的员工名和入职日期
select last_name, date_format(hiredate, '%m月/%d日 %Y年') 入职日期
from employees
where commission_pct is not null;
```

##### 其他函数

```mysql
/*version版本
database当前库
user当前连接用户*/

select version();
select database();
select user();
```

##### 流程控制函数

```mysql
/*if 处理双分支
case语句 处理多分支
		情况1：处理等值判断
		情况2：处理条件判断*/
		
select if(10 > 5, '大', '小') aa;
select last_name, commission_pct,  if(commission_pct is null, '没奖金', '有奖金') aa
from employees;

/*
case的使用法一：

case 要判断的字段或表达式
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值2或语句2
when 条件3 then 要显示的值3或语句3
......
else 要显示的值n或语句n
end 【as 别名】
*/
select salary 原始工资, department_id,
case department_id
when 30 then salary*1.1
when 40 then salary*1.2
when 50 then salary*1.3
else salary
end as 新工资
from employees;

/*
case的使用法二：

case 
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值2或语句2
when 条件3 then 要显示的值3或语句3
......
else 要显示的值n或语句n
end 【as 别名】
*/
select salary 原始工资, department_id,
case
when salary>20000 then 'A'
when salary>15000 then 'B'
when salary>10000 then 'C'
else 'D'
end as 工资级别
from employees;
```

#### **分组函数**

```mysql
/*sum 求和
max 最大值
min 最小值
avg 平均值
count 计数

特点：
	1、以上五个分组函数都忽略null值且不计入总数，
	2、sum和avg一般用于处理数值型
		max、min、count可以处理任何数据类型
    3、都可以搭配distinct使用，用于统计去重后的结果
	4、count的参数可以支持：字段、*、常量值，一般放1
		count(*)，计算行数时null的也会包含进去，效率高，最常用
		count(1)，计算行数时null的也会包含进去，效率高
		count(字段名)，得到的结果将是除去值为null和重复数据后的结果，效率低
	5、和分组函数一同查询的字段要求是group by后的字段
*/

select sum(salary) from employees;
select max(salary) from employees;
select min(salary) from employees;
select avg(salary) from employees;
select count(salary) from employees;
#组合
select sum(salary), max(salary), round(avg(salary)) from employees;
#查询部门编号为90的员工个数
select count(*) as 个数
from employees
where department_id = 90;
```

##### 分组查询

```mysql
/*语法：
	select 查询的字段，分组函数
	from 表
	【where 筛选条件】
	group by 分组的字段
	【order by 子句】
*/
/*
特点：
	1、可以按单个字段分组
	2、和分组函数一同查询的字段最好是分组后的字段
	3、分组筛选
			            针对的表	         位置			    关键字
	      分组前筛选：	原始表		      group by的前面		where
	      分组后筛选：	分组后的结果集	   group by的后面		 having

	4、可以按多个字段分组，字段之间用逗号隔开
	5、可以支持排序
	6、having后可以支持别名
*/


#查询每个工种的最高工资
select max(salary), job_id
from employees
group by job_id;

#查询每个位置的部门个数
select count(*), location_id
from departments
group by location_id;

#查询邮箱中包含a字符的，每个部门的平均工资
select avg(salary), department_id, email
from employees
where email like '%a%'
group by department_id;

#查询有奖金的每个领导手下员工的最高工资
select max(salary), manager_id
from employees
where commission_pct is not null
group by manager_id;

#查询哪个部门的员工个数大于2
select count(*), department_id
from employees
group by department_id
having count(*) > 2;

#查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
select job_id, max(salary)
from employees
where commission_pct is not null
group by job_id
having max(salary) > 12000;

#查询领导编号大于102的每个领导手下的最低工资>5000的领导编号是哪个，以及其最低工资
select manager_id, min(salary)
from employees
where manager_id > 102
group by manager_id
having min(salary) > 5000;

#按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些
select length(concat(first_name, last_name)) 长度, count(*)
from employees
group by length(concat(first_name, last_name))
having count(*) > 5;

#查询每个部门，每个工种员工的平均工资
select avg(salary), department_id, job_id
from employees
group by department_id, job_id;

#查询每个部门，每个工种员工的平均工资，平均工资大于10000，并且按平均工资的高低显示出来
select avg(salary), department_id, job_id
from employees
where department_id is not null
group by department_id, job_id
having avg(salary) > 10000
order by avg(salary) desc;
```
