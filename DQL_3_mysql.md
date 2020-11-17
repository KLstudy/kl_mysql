# MySQL

## 子查询

概述：出现在其他语句中的select语句，称为自查询，或者成为内查询

​    外部的查询语句，称为主查询

分类：

按子查询出现的位置：

​        select 后面

​				仅支持标量子查询

​		from 后面

​				支持表子查询

​		where或having后面（*****）

​				标量子查询-单行（*****）

​				列子查询-多行（*****）

​				行子查询

​		exists 后面（相关子查询）

​				表子查询

按结果集的行列数不同：

​		标量子查询（结果集只有一行一列）

​		列子查询（结果集只有一列多行）

​		行子查询（结果集可以有多行多列）

​		表子查询（结果集一般为多行多列）

### where和having后面

#### 特点：



1. 都会放在（）内

2. 子查询一般放在条件右侧

3. 标量子查询，一般搭配着单行操作符使用

   ``` > < >= <= = <>``` 

4. 列子查询，一般搭配着多行操作符使用

   ``` IN,ANY/SOME,ALL```

    

#### 标量子查询（单行子查询）

```mysql
#案例一：谁的工资比Abel高？
use myemployees;
select *
from employees
where salary>(
	select salary
	from employees
	where last_name='Abel'
);

/*
个人理解：原来单列指的是子查询结果为单列而不是整体查询结果为单列
*/
```

```mysql
#案例二： 返回job_id与141号员工相同，salary比143号员工多的员工的姓名，job_id和工资
select last_name,job_id,salary
from employees
where job_id=(
	select job_id
	from employees
	where employee_id=141
) and salary>(
	select salary
	from employees
	where employee_id=143
);
```

```mysql
#案例三：返回公司工资最少的员工的last_name,job_id和salary
select last_name,job_id,salary
from employees
where salary=(
	select min(salary)
	from employees
);
```

```mysql
# 查询最低工资大于60号部门最低工资的部门id和最低工资
select min(salary),department_id
from employees
group by department_id
having min(salary)>(
	select min(salary)
	from employees
	where department_id=60
);
```

#### 列子查询（多行子查询）

IN/NOT IN（*****）：等于列表中的任意一个

```mysql
# 返回location_id是1400或1700的部门中的所有员工姓名
#1,查询location_id是1400或者1700的部门编号
select department_id
from departments
where location_id in (1400,1700);
#2，查询department_id在1结果中的员工姓名
select last_name
from employees
where department_id in (
	select department_id
	from departments
	where location_id in (1400,1700)
);

```



ANY/SOME：和子查询返回的某一个值比较

ALL：和子查询返回的所有值比较

#### 行子查询（多列多行子查询）

使用相对较少，了解。

要求多个字段之间都可以用同一个条件进行连接

```mysql
#案例一：查询员工编号最小，且工资最高的员工信息
select *
from employees
where (employee_id,salary)=(
	select min(employee_id),max(salary)
    from employees
);

```

### select 后面

```mysql
#案例：查询每个部门的员工个数 
select d.*,(
	select count(*)
    from employees as e
    where e.department_id = d.department_id
) as 员工个数
from departments as d;

```

### from 后面

将子查询的结果集作为主查询的表

### exists后面（相关子查询）

判断子查询结果是否有值

exists(完整的查询语句)

结果：0或1

## 分页查询

### 概述

引入：当我们要显示的数据在一页难以显示的时候，我们就需要分页查询

### 语法

```
select 查询列表
from 表
[join type] join表2
on 连接条件
where 筛选条件
group by 分组字段
having 分组后的筛选
order by 排序的字段
limit offset,size;

offset:要显示的条目的起始索引（从0开始）
size：要显示的条目个数

```

### 特点

1. limit语句放在查询语句的最后

2. 关于起始索引的公式

   要显示的页数page，每页的条目数size

   ``` 
   select 查询列表
   from 表
   limit (page-1)*size,size;
   ```

   

### 练习

```mysql
#案例： 查询前五条员工信息 
select *
from employees
limit 0,5;
```







