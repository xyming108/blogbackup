---
title: mysql学习笔记Day3
date: 2021-03-18 09:16:24
tags:
- mysql
- 数据库
categories: 数据库
description: mysql数据库多表连接查询、子查询、分页查询、联合查询
top_img:
cover: https://gitee.com/xyming108/blog_img/raw/master/wallpaper/b6.jpg
---





# Day3

### **1、多表连接查询**

```mysql
/*
按功能分类：
	内连接：
		等值连接
		非等值连接
		自连接
	外连接：
		左外连接
		右外连接
		全外连接
	交叉连接
*/
```

#### 内连接（sql92语法）

##### 等值连接

```mysql
#等值连接
#查询员工名和对应的部门名
select last_name, department_name
from employees, departments
where employees.`department_id` = departments.`department_id`;

#为表起别名：提高语句的简洁度，区分重名字段（注意：起了别名后不能用原来的表名）
#查询员工名、工种号、工种名
select last_name, e.job_id, job_title
from employees as e, jobs as j
where e.job_id = j.job_id;

#查询有奖金的员工名、部门名
select last_name, department_name, commission_pct
from employees e, departments d
where e.department_id = d.department_id and e.commission_pct is not null;

#查询城市名中第二个字符为o的部门名和城市名
select department_name, city
from departments d, locations l
where d.location_id = l.location_id and city like '_o%';

#查询每个城市的部门个数
select count(*) 个数, city
from departments d, locations l
where d.location_id = l.location_id
group by city;

#查询每个工种的工种名和员工的个数，并且按员工个数降序
select job_title, count(*)
from employees e, jobs j
where e.job_id = j.job_id
group by job_title
order by count(*) desc;

#三表连接
#查询员工名、部门名、所在城市
select last_name, department_name, city
from employees e, departments d, locations l
where e.department_id = d.department_id
and d.location_id = l.location_id;
```

##### 非等值连接

```mysql
#查询员工的工资和工资级别
select salary, grade_level
from employees e, job_grades j
where salary between j.lowest_sal and j.highest_sal;
```

##### 自连接

```mysql
#查询员工名和其对应的上级的名称（查员工名要访问一次表，查领导名要再访问一次同一张表）
select e.employee_id, e.last_name, m.manager_id, m.last_name
from employees e, employees m
where m.manager_id = e.employee_id;
```

### **2、sql99语法**

```mysql
/*语法
select 查询列表
from 表1 别名
【inner|left outer|right outer|cross】 表2 别名 on 连接条件2
【inner|left outer|right outer|cross】 表3 别名 on 连接条件3
【where 筛选条件】
【group by 分组】
【having 分组后的筛选条件】
【order by 排序列表】

分类：
	内连接：inner
	外连接：
		左外连接：left 【outer】
		右外连接：right 【outer】
		全外连接：full 【outer】
	交叉连接：cross
*/


```

#### 内连接

```mysql
/*语法

select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件

分类：
	等值
	非等值
	自连接
*/
```

##### 等值连接

```mysql
#查询员工名、部门名
select last_name, department_name
from employees e
inner join departments d
on e.department_id = d.department_id;

#查询名字中包含e的员工名和工种名
select last_name, job_title
from employees e
inner join jobs j
on e.job_id = j.job_id
where e.last_name like '%e%';

#查询部门个数 > 3 的城市名和部门个数
select city, count(*) 
from departments d
inner join locations l
on l.location_id = d.location_id
group by city
having count(*) > 3;

#查询员工名、部门名、工种名，并按部门名降序
select last_name, department_name, job_title
from employees e
inner join departments d on d.department_id = e.department_id
inner join jobs j on j.job_id = e.job_id
order by department_name desc;
```

##### 非等值连接

```mysql
#查询员工的工资级别
select salary, grade_level
from employees e
join job_grades g
on e.salary between g.lowest_sal and g.highest_sal;

#查询每个工资级别的个数>20的个数，并且按工资级别降序
select count(*), grade_level
from employees e
join job_grades g
on e.salary between g.lowest_sal and g.highest_sal
group by grade_level
having count(*) > 20
order by grade_level desc;
```

##### 自连接

```mysql
#自连接
#查询员工的名字、上级的名字
select e.last_name, m.last_name
from employees e
join employees m
on e.manager_id = m.employee_id;
```

#### 外连接

```mysql
/*
应用场景：用于查询一个表中有，另一个表中没有的记录

特点：
	1、外连接的查询结果为主表中的所有记录
		如果从表中有和它匹配的，则显示匹配的值
		如果从表中没有和它匹配的，则显示null
		外连接查询结果 = 内连接结果 + 主表中有而从表中没有的记录
	2、左外连接，left join左边的是主表
	   右外连接，right join右边的是主表
	3、左外和右外交换两个表的顺序，可以实现同样的效果
	4、全外连接 = 内连接的结果 + 表1中有但表2中没有的 + 表2中有但表1中没有的
*/
```

```mysql
#引入：查询男朋友不在男神表的女神名
select * from beauty;
select * from boys;
#左外
select b.name, bo.*
from beauty b
left outer join boys bo
on b.boyfriend_id = bo.id
where bo.id is null;
#右外，与上结果同
select b.name, bo.*
from boys bo
right outer join beauty b
on b.boyfriend_id = bo.id
where bo.id is null;
#交叉连接(笛卡尔乘积)
select b.*, bo.*
from beauty b
cross join boys bo;
```



### **3、子查询**

```mysql
/*
含义：
	一条查询语句中又嵌套了另一条完整的select语句，其中被嵌套的select语句，称为子查询或内查询，在外面的查询语句，称为主查询或外查询

分类：
按子查询出现的位置：
	select后面：
		仅支持标量子查询
	from后面：
		支持表子查询
	where或having后面：
		标量子查询（单行）
		列子查询（多行）
		行子查询
	exists后面（相关子查询）
		表子查询
按结果集的行列数不同：
	标量子查询（结果集只有一行一列）
	列子查询（结果集只有一列多行）
	表子查询（结果集一般为多行多列）
*/
```

#### where或having后面

```mysql
/*
一、where或having后面
	1、标量子查询（单行子查询）
	2、列子查询（多行子查询）
	3、行子查询（多行多列）
	
特点：
    1、子查询都放在小括号内
    2、子查询可以放在from后面、select后面、where后面、having后面，但一般放在条件的右侧
    3、子查询优先于主查询执行，主查询使用了子查询的执行结果
    4、子查询根据查询结果的行数不同分为以下两类：
        ①单行子查询
            结果集只有一行
            一般搭配单行操作符使用：> < = <> >= <= 
            非法使用子查询的情况：
            	a、子查询的结果为一组值
            	b、子查询的结果为空
        ② 多行子查询
            结果集有多行
            一般搭配多行操作符使用：any、all、in、not in
            in： 属于子查询结果中的任意一个就行
            any和all往往可以用其他查询代替 
*/


#1、标量子查询（单行子查询）------------------------------------------------
#①查询Abel的工资
select salary
from employees
where last_name = 'Abel';
#查询员工的信息，满足 salary>①结果
select *
from employees
where salary > (
	select salary
	from employees
	where last_name = 'Abel'
);

#②返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id和工资
#第一步：①查询141号员工的job_id
select job_id
from employees
where employee_id = 141;
#第二步：②查询143号员工的salary
select salary
from employees
where employee_id = 143;
#第三步：查询员工的姓名，job_id和工资，要求job_id=①并且salary>②
select last_name, job_id, salary
from employees
where job_id=(
	select job_id
	from employees
	where employee_id = 141
) and salary > (
	select salary
	from employees
	where employee_id = 143
);

#查询最低工资大于50号部门最低工资的部门id和其最低工资
select min(salary), department_id
from employees
group by department_id
having min(salary) > (
	select min(salary)
    from employees
    where department_id = 50
);



#2、列子查询（多行子查询）------------------------------------------------
#返回location_id是1400或1700的部门中的所有员工姓名
select last_name
from employees
where department_id in(
	select distinct department_id
    from departments
    where location_id in(1400, 1700)
);
#或
select last_name
from employees
where department_id =any(
	select distinct department_id
    from departments
    where location_id in(1400, 1700)
);

#返回location_id不是1400或1700的部门中的所有员工姓名
select last_name
from employees
where department_id not in(
	select distinct department_id
    from departments
    where location_id in(1400, 1700)
);
#或
select last_name
from employees
where department_id <>all(
	select distinct department_id
    from departments
    where location_id in(1400, 1700)
);


#返回其他部门（意思是除去部门IT_PROG的）中比job_id为'IT_PROG'工种任一工资低的员工的员工号、姓名、job_id以及salary
#一、先查询job_id为'IT_PROG'工种任一工资
select distinct salary
from employees
where job_id = 'IT_PROG';
#二、查询员工号、姓名、job_id以及salary，salary < (一)的任意一个
select last_name, employee_id, job_id, salary
from employees
where salary < any(
	select distinct salary
	from employees
	where job_id = 'IT_PROG'
) and job_id <> 'IT_PROG';
#或
select last_name, employee_id, job_id, salary
from employees
where salary < (
	select  max(salary)
	from employees
	where job_id = 'IT_PROG'
) and job_id <> 'IT_PROG';

#返回其他部门（意思是除去部门IT_PROG的）中比job_id为'IT_PROG'工种所有工资低的员工的员工号、姓名、job_id以及salary
select last_name, employee_id, job_id, salary
from employees
where salary < all(
	select distinct salary
	from employees
	where job_id = 'IT_PROG'
) and job_id <> 'IT_PROG';
#或
select last_name, employee_id, job_id, salary
from employees
where salary < (
	select  min(salary)
	from employees
	where job_id = 'IT_PROG'
) and job_id <> 'IT_PROG';

#3、行子查询（多行多列）------------------------------------------------
#查询员工编号最小并且工资最高的员工信息
select *
from employees
where (employee_id, salary) = (
	select min(employee_id), max(salary)
    from employees
)
```

#### select后面



#### from后面

```mysql
#查询每个部门的平均工资的工资等级
#(将子查询结果充当一张表，要求必须其别名)
select ag_dep.*, g.grade_level
from (
	select avg(salary) ag, department_id
	from employees
	group by department_id
) ag_dep
inner join job_grades g
on ag_dep.ag between lowest_sal and highest_sal;
```

#### exists后面

```mysql
#结果为0或1，判断是否存在
select exists(
	select employee_id
    from employees
    where salary=300000
);

#查询有员工的部门名
select department_name
from departments d
where exists(
	select *
    from employees e
    where d.department_id = e.department_id
);
#或
select department_name
from departments d
where d.department_id in(
	select department_id
    from employees
);

#查询没有女朋友的男神信息
use girls;
select bo.*
from boys bo
where bo.`id` not in(
	select boyfriend_id
    from beauty
);
#或
select bo.*
from boys bo
where not exists(
	select boyfriend_id
    from beauty b
    where bo.`id`=b.`boyfriend_id`
);
```

### **4、分页查询**

```mysql
/*语法：
select 字段|表达式,...
from 表
【where 条件】
【group by 分组字段】
【having 条件】
【order by 排序的字段】
limit 【offset起始的条目索引，】 size条目数;

特点：
	1.起始条目索引从0开始
	2.limit子句放在查询语句的最后
	3.公式：select * from  表 limit （page-1）*sizePerPage,sizePerPage
            假如:
            每页显示条目数sizePerPage
            要显示的页数 page
*/
#查询前五条员工信息
select * from employees limit 0, 5;

#查询第11条到第25条
select * from employees limit 10, 15;

#有奖金的员工信息，并且工资较高的前10名显示出来
select *
from employees
where commission_pct is not null
order by salary desc
limit 10;
```

### **5、联合查询**

```mysql
/*
语法：
select 字段|常量|表达式|函数 【from 表】 【where 条件】 union 【all】
select 字段|常量|表达式|函数 【from 表】 【where 条件】 union 【all】
select 字段|常量|表达式|函数 【from 表】 【where 条件】 union  【all】
.....
select 字段|常量|表达式|函数 【from 表】 【where 条件】

特点：
	1、多条查询语句的查询的列数必须是一致的
	2、多条查询语句的查询的列的类型和顺序要一致
	3、union代表去重，union all代表不去重
*/

#查询部门编号>90或者邮箱包含a的员工信息
select *
from employees where email like '%a%' or department_id > 90;
#等同于
select * from employees where email like '%a%'
union
select * from employees where department_id > 90;

#查询中国用户中男性的信息以及外国用户中年男性的用户信息
select id, cname, csex from t_ca where csex='男'
union
select t_id, tName, tGender from t_ua where tGender = 'male';
```