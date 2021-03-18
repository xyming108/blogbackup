---
title: mysql学习笔记Day4
date: 2021-03-18 09:18:12
tags:
- mysql
- 数据库
categoreies: 数据库
description: mysql数据库的DML语言、DDL语言、TCL语言、视图
top_img:
cover: https://gitee.com/xyming108/blog_img/raw/master/wallpaper/b7.jpg
---

[TOC]





# **Day4**

### **1、DML语言**

#### 插入

```mysql
/*
语法：
	方式一：（支持插入多行，子查询）
		insert into 表名(字段名，...)
		values(值1，...), (值1，...), ......;
	方式二：（不支持插入多行，不支持子查询）
		insert into 表名
		set 列名=值, 列名=值, ......;

特点：
    1、字段类型和值类型一致或兼容，而且一一对应
    2、可以为空的字段，可以不用插入值，或用null填充
    3、不可以为空的字段，必须插入值
    4、字段个数和值的个数必须一致
    5、字段可以省略，但默认所有字段，并且顺序和表中的存储顺序一致
*/

#方式一：多行
insert into beauty(id, name, sex, borndate)
values(13, '唐艺昕', '女', '1990-4-3'), (14, '刘亦菲', '女', '1987-4-3');

#方式一：子查询（会把结果插入对应的字段中）
insert into beauty(id, name, phone)
select 26, '宋茜', '88888888888'

#方式二：
insert into beauty
set id=13, name='唐嫣', sex='女', borndate='1998-9-1';



```

#### 修改

```mysql
/*
修改单表语法：
	update 表名 
	set 字段=新值,字段=新值
	【where 条件】
	
修改多表语法：
	update 表1 别名1,表2 别名2
	set 字段=新值，字段=新值
	where 连接条件
	and 筛选条件
*/

#修改beauty表中姓唐的女神的电话为88888888888, 
update beauty
set phone='88888888888'
where name like '唐%';

#修改张无忌女朋友的手机号为123
update boys bo
inner join beauty b
on bo.id = b.boyfriend_id
where bo.boyName='张无忌';
```

#### 删除

```mysql
/*
单表的删除：
	delete from 表名 【where 筛选条件】

多表的删除：
	delete 别名1，别名2
	from 表1 别名1，表2 别名2
	where 连接条件
	and 筛选条件;

	truncate语句：（又称清空，一次删除整个表，不能加where条件）
		truncate table 表名
		
区别：
	1.truncate不能加where条件，而delete可以加where条件
	2.truncate的效率高一丢丢
	3.truncate 删除带自增长的列的表后，如果再插入数据，数据从1开始delete 删除带自增长列的表后，如果再插入数据，数据从上一次的断点处开始
	4.truncate删除不能回滚，delete删除可以回滚
*/

#删除手机号以9结尾的女神信息
delete from beauty where phone like '%9';

#删除张无忌女朋友的信息
delete b
from beauty b
inner join boys bo
on b.boyfriend_id = bo.id
where bo.boyName = '张无忌';

#删除黄晓明的信息和他女朋友的信息
delete b, bo
from beauty b
inner join boys bo
on b.boyfriend_id = bo.id
where bo.boyName = '黄晓明';
```

### **2、DDL语言**

#### 库的管理

```mysql
#库的管理
    一、创建库
    create database (if not exists) 库名;
    二、删除库
    drop database (if exists) 库名;
    三、库的修改
    alter database 旧库名 to 新库名;
```

#### 表的管理

```mysql
#表的管理
	一、表的创建
        create table 表名(
            列名 列的类型 【(长度)约束】,
            列名 列的类型 【(长度)约束】,
            列名 列的类型 【(长度)约束】,
            ...
        );
        例如：
        CREATE TABLE IF NOT EXISTS stuinfo(
            stuId INT,
            stuName VARCHAR(20),
            gender CHAR,
            bornDate DATETIME
        );
	二、表的修改
		1、修改列名
			alter table 表名 change 【column】 旧列名 新列名 类型;
			例如：
			alter table book change column publishdate pubdate datetime;
		2、修改列的类型或约束
			alter table 表名 modify column 列名 类型;
			例如：
			alter table book modify column pubdate timestamp;
		3、添加新列
			alter table 表名 add column 列名 类型;
			例如：
			alter table author add column annual double;
		4、删除列
			alter table 表名 drop column 列名 类型;
			例如：
			alter table author drop column annual double;
		5、修改表名
			alter table 旧表名 rename to 新表名;
			例如：
			alter table author rename to zuozhe;
	三、表的删除
		drop table 【if exists】 表名;
	四、表的复制
		1、只复制表的结构，不复制数据
			create table 新表名 like 被复制表名;
			例如：
			create table copy_author like author;
		2、复制表的结构 + 全部数据
			create table 新表名 select * from 被复制表名;
			例如：
			create table copy_author select * from author;
		3、复制表的结构 + 部分数据
			create table 新表名
			select 列名, 列名, ...
			from 被复制表名
			【where 筛选条件】 ;
		4、只复制部分结构
			create table 新表名
			select 列名, 列名, ...
			from 被复制表名
			where 0;
		
```

#### 数据类型

##### 数值类型

```
1、整数
	特点：
		a.默认是有符号，无符号需添加unsigned关键字
		b.若插入的数值超出范围，会报out of range异常， 并且插入临界值
		c.如果不设置长度，会有默认长度，长度代表了显示的最大宽度，如果不够会用0在左边填充，但必须搭配zerofill使用
2、小数
	分类：
		a.浮点型
			float(M, D)
			double(M, D)
		b.定点型
			dec(M, D)
			decimal(M, D)
	特点：
		M：整数部位长度+小数部位长度
		D：小数部位长度
	注意：M和D都可以省略，如果是decimal，则M默认是10， D默认是0
		 			   如果是float和double，则会根据插入的数值精度来决定精度
	总结：定点型的精度较高，如果要求插入数值的精度较高如货币运算等可以考虑使用
```

##### 字符串类型

```mysql
1、较短的文本（M为字符数）
	char(M)       固定长度的字符    空间耗费大，但效率高
	varchar(M)	  可变长度的字符    空间耗费小，但效率低
2、较长的文本
	text
	blob

#--------------------------------------------------------------------
enum类型：
# 又称枚举类型，要求插入的值必须属于列表中指定的值之一

create table tab_char(
	c enum('a', 'b', 'c')
);

insert into tab_char values('a');
insert into tab_char values('b');
insert into tab_char values('c');
insert into tab_char values('A');
insert into tab_char values('m');  #报错，因为不包含在枚举列表中

#---------------------------------------------------------------------
set类型
# 和enum类型类似，区别在于set类型一次可以选取多个成员，而enum只能选取一个成员

create table tab_set(
	s set('a', 'b', 'c', 'd')
);

insert into tab_set values('a');
insert into tab_set values('a, b');
insert into tab_set values('A, b, c');
```

#### 常见约束

##### 创建表时添加约束

```mysql
#含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确性和可靠性
/*
常见六大约束：
    NOT NULL			保证字段不为空
    DEFAULT				保证字段有默认值
    UNIQUE				唯一，保证字段值具有唯一性，可以为空
    CHECK				检查约束（mysql不支持）
    PRIMARY KEY		     主键，保证字段值具有唯一性，不能为空
    FOREIGN KEY			 外键，用于限制两个表的关系，保证该字段的值必须来自于主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值
*/

#创建表时添加约束
一、添加列级约束
/*	语法：
		直接在字段名和类型后面追加约束类型即可
		只支持：默认、非空、主键、唯一
*/
		
create table stuinfo(
	id int primary key,		#主键
    stuName varchar(20) not null,		#非空
    gender char(1) check(gender='男' or gender='女'),		#检查
    seat int unique,		#唯一
    age int default,		#默认约束
    majorId int references(id)		#外键
);

二、添加表级约束
/*
语法：在各个字段的最下面
	【constraint 约束名】 约束类型(字段名);
*/

create table stuinfo(
	id int,
    stuname varchar(20),
    gender char(1),
    seat int,
    age int,
    majorid int,
    
    constraint pk primary key(id),		#主键
    constraint uq unique(seat),			#唯一键
    constraint ck check(gender='男' or gender='女'),		#检查
    constraint fk_stuinfo_major foreign key(majorid) references major(id) #外键
);

三、通用写法
create table if not exists stuinfo(
	id int primary key,
    stuName varchar(20) not null,
    sex char(1),
    seat int unique 18,
    age int default,
    majorId int,
    constraint fk_stuinfo_major foreign key(majorid) references major(id)
);

/*
主键和唯一的对比：
		唯一性		是否允许为空		一个表中可以有多少个		是否允许组合
主键		√			×				一个					允许（不推荐）
唯一		√			√				多个					允许（不推荐）

外键：
	1、要求在从表设置外键关系
	2、从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
	3、主表的关联列必须是一个key（一般是主键或唯一）
	4、插入数据时，先插入主表，再插入从表；删除数据时，先删除从表，再删除主表

*/
```

##### 修改表时添加约束

```mysql
/*
1、添加列级约束
	alter table 表名 modify column 字段名 字段类型 新约束;
2、添加表级约束
	alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
*/


alter table stuinfo modify column stuname varchar(20) not null;

alter table stuinfo modify column age int default 18;

alter table stuinfo modify column id int primary key;
alter table stuinfo add primary key(id);

alter table stuinfo modify column seat int unique;
alter table stuinfo add unique(seat);

alter table stuinfo add foreign key(majorid) references major(id);

```

##### 修改表时删除约束

```mysql
alter table stuinfo modify column stuname varchar(20) null;

alter table stuinfo modify column age int;

alter table stuinfo drop primary key;

alter table stuinfo drop index seat;

alter table stuinfo drop foreign key fk_stuinfo_major;
```

#### 标识列

```mysql
/*
又称自增长列，可以不用手动插入值，系统提供默认的序列值
*/

/*
1、标识列不一定要和主键搭配，但要求是一个key
2、一个表最多可以有一个标识列
3、标识列的类型只能是数值型
4、标识列可以通过  set auto_increment_increment=3; 设置步长，可以手动插入值，设置起始值
*/

一、创建表时设置标识列
create table tab_identity(
	id int primary key auto_increment,
    name varchar(20)
);
#会自动创建1-20的id
insert into tab_identity(id, name) values(null, 'john');

二、修改表时设置标识列
alter table tab_identity modify column id int primary key auto_increment;

三、修改表时删除标识列
alter table tab_identity modify column id int;
```

### **3、TCL语言**

#### 事务

```mysql
/*
transaction control language：事务控制语言
事务：
	一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行
*/
/*
事务的特性：
	原子性：一个事务不可再分割，要么都执行要么都不执行
	一致性：一个事务执行会使数据从一个一致状态切换到另外一个一直状态
	隔离性：一个事务的执行不受其他事务的干扰
	持久性：一个事务一旦提交，则会永久的改变数据库的数据

事务的创建：
	隐式事务：事务没有明显的开启或结束的标记，比如insert、update、delete语句
	显示事务：事务具有明显的开启或结束的标记，前提：必须先设置自动提交功能为禁用
			set autocommit=0;
			
			步骤1：开启事务
				set autommit=0;
				start transaction;（可选）
			步骤2：编写事务中的sql语句（select、insert、update、delete）
				语句1;
				语句2;
				...
			步骤3：结束事务
				commit; （提交事务）
				rollback; （回滚事务）
*/

set autocommit=0;
start transaction;
update account set balance = 500 where username='张无忌';
update account set balance = 1500 where username='赵敏';
commit;


/*
事务并发问题如何发生？
	当多个事务同时操作同一个数据库的相同数据时
	
事务的并发问题有哪些？
	脏读：一个事务读取到了另外一个事务未提交的数据
	不可重复读：同一个事务中，多次读取到的数据不一致
	幻读：一个事务读取数据时，另一个事务进行更新，导致第一个事务读取到了没有更新的数据

如何避免事务的并发问题？
	通过设置事务的隔离级别
	1、read uncommitted
	2、read committed 可以避免脏读
	3、repeatable read 可以避免脏读、不可重复读和一部分幻读
	4、serializable 可以避免脏读、不可重复读和幻读

设置隔离级别：
	set session|global  transaction isolation level 隔离级别名;

查看隔离级别：
	select @@tx_isolation;
*/

```

### **4、视图**

```mysql
#视图
/*
含义：
	虚拟表，和普通表一样使用
好处：
	1、sql语句提高重用性，效率高
	2、和表实现了分离，提高了安全性
视图和表的区别：
			  使用方式	   占用物理空间				语法

		视图	 完全相同	不占用，仅仅保存的是sql逻辑    create view
		表	  完全相同	 占用						  create table
*/
一、视图的创建
#查询姓名中包含a字符的员工名、部门名和工种信息
1、创建
create view myview
as
select last_name, department_name, job_title
from employees e
join departments d on e.department_id = d.department_id
join jobs j on e.job_id = j.job_id;
2、使用
select * from myview where last_name like '%a%';

#查询平均工资最低的部门名和工资
create view myv2
as
select avg(salary) ag
from employees
group by department_id;

create view myv3
as
select * from myv2
order by ag
limit 1;

select d.*, m.ag
from myv3 m
join departments d
on m.department_id = d.department_id;

二、视图的修改
/*
	方式一：
        create or replace view 视图名
        as
        查询语句
*/
create or replace view myv3
as
select avg(salary), job_id
from employees
group by job_id;

/*
	方式二：
        alter view 视图名
        as
        查询语句
*/
alter view myv3
as
select * from employees;

三、视图的删除
/*
	语法：
		drop view 视图名, 视图名, ...
*/
drop view myv1, myv2, myv3;

四、视图的查看
/*
	语法：
		show create view 视图名;
*/

五、视图的更新（更改数据）
	1、插入
		insert into myv1 values('张飞', 'zf@qq.com');
	2、修改
		update myv1 set last_name = '张无忌' where last_name = '张飞';
	3、删除
		delete from myv1 where last_name = '张无忌';
```

### **5、变量**

```mysql
/*
系统变量：
	全局变量：服务器每次启动将为所有的全局变量赋初值，针对于所有的会话有效，但不能			    跨重启
	会话变量：仅仅针对当前会话有效
自定义变量：
	用户变量：针对于当前会话有效，应用在任何地方，即begin end里面或begin end外面
	局部变量：仅仅在它的begin end中有效，应用在begin end中的第一句话
*/
一、系统变量
说明：由系统提供，属于服务器层面
1、查询所有的系统变量
show global|【session】 variables;
2、查看满足条件的部分系统变量
show global|【session】 variables like '%char%';
3、查看指定的某个系统变量的值
select @@global|【session】.系统变量名;
4、为某个系统变量赋值
set global|【session】 系统变量名=值;
set @@global|【session】.系统变量名=值;


二、自定义变量
1、说明：用户自定义变量
    使用步骤：
    声明->赋值->使用
    1、声明并初始化
    set @用户变量名=值; 
    或
    set@用户变量名:=值; 
    或
    select @用户变量名:=值;

    2、赋值（更新用户变量的值）
    方式一：
    set @用户变量名=值; 
    或
    set@用户变量名:=值; 
    或
    select @用户变量名:=值;
    方式二：
    select 字段 into 变量名
    from 表;

    3、使用
    select @用户变量名;

    例如：
        set @name='john';
        set @name=100;
        set @count=1;

        select count(*) into @count
        from employees;

        select @count;
    
2、局部变量：
	1、声明
		declare 变量名 类型;
		declare 变量名 类型 default 值;
	2、赋值
		方式一：通过 set 或 select
			set 局部变量名=值;
			或
			set 局部变量名:=值;
			或
			select @局部变量名:=值;
		方式二：通过 select into
			select 字段 into 局部变量名
			from 表;
	3、使用
		select 局部变量名;
		
	例如：
		declare m int default 1;
		declare n int default 2;
		declare sum int;
		set sum=m+n;
		select sum;
```

### **6、存储过程和函数**

#### 存储过程

```mysql
含义：一组预先编译好的SQL语句的集合，理解成批处理语句
	1、提高代码的重用性
	2、简化过程
	3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

一、创建语法
create procedure 存储过程名(参数列表)
begin
	存储过程体（一组合法的SQL语句）
end
注意：
1、参数列表包含三部分
    参数模式 参数名 参数类型

    例如： in stuname varchar(20)

    参数模式：
        in：该参数可以作为输入，需要调入方传入值
        out：该参数可以作为输出，该参数可以作为返回值
        inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值

2、如果存储过程体仅仅只有一句话， begin end 可以省略
	存储过程体中的每条SQL语句的结尾要求必须加分号
	存储过程的结尾可以使用 delimiter 重新设置
	语法：
		delimiter 结束标记
		例如：
		delimiter $

二、调用语法
call 存储过程名（实参列表）;


三、参数前面的符号的意思
in:该参数只能作为输入 （该参数不能做返回值）
out：该参数只能作为输出（该参数只能做返回值）
inout：既能做输入又能做输出
```