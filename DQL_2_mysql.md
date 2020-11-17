# MySQL

## 连接查询

概述：又称为多表查询，当查询的字段来自多个表时，就会用到连接查询

#### 笛卡尔乘积现象

概述：假设表1有m行，表2有n行，结果等于m*n行

原因：没有有效的连接条件

```mysql
select count(*) from beauty;
#这里输出12行
select count(*) from boys;
#这里输出4行
select name,boyname from boys,beauty
#那么最终结果就是48行
```

```mysql
select count(*) from beauty;
select count(*) from boys;
select name,boyname from boys,beauty
where beauty.boyfrinend_id=boys.id;
#这里的就正常了
```

#### 连接查询的分类

##### 按年代分类

###### sql 92标准

在mysql中仅支持内链接

###### sql 99标准【推荐】

支持所有的内连接

外连接（左外+右外）

交叉连接

##### 按功能分类

###### 内连接

###### 外连接

###### 交叉连接

### 连接查询的基本使用

#### sql 92 标准

##### 特点

1. 多表连接的结果是多表的交集部分
2. n表连接最少需要n-1个连接条件
3. 多表连接的顺序没有要求
4. 一般需要为表起别名
5. 可以搭配之前学习的各种查询一同使用

##### 等值连接

```mysql
select name,boyname from boys,beauty
where beauty.boyfrinend_id=boys.id;
#这就是一个等值连接
```

```mysql
#案例：查询员工名对应的部门名
select last_name,department_name
from employees,departments
where employees.`department_id`=departments.`department_id`;
```

由于一般情况下表名都会比较长，但是在连接查询中又经常会用表明去限定某些参数，因此，我们可以给表起别名，要注意的是，如果起了别名，在语句中就不能使用表名去限定了

```mysql
select last_name,department_name
from employees as e,departments as d
where e.`department_id`=d.`department_id`;
```

```mysql
#案例：查询有奖金的员工名，部门名
select last_name,department_name,commission_pct
from employees as e,departments as d
where e.`department_id`=d.`department_id`
and e.commission_pct is not null;
```

```mysql
#查询城市名中第二个字符为o的员工的部门名和城市名
select department_name,city
from departments as d,locations as l
where d.`location_id`=l.`location_id`
and city like '_o%';
```

```mysql
#案例： 查询每个城市的部门个数 
select count(*),city
from departments as d,locations as l
where d.`location_id`=l.`location_id`
group by city;
```

##### 非等值连接

涉及到一个表中的某个元素要对应另一个表中的多个元素，可以用到非等值连接

##### 自连接

自己连接自己

```mysql
#案例： 查询员工和上级的名称
select e.employee_id,e.last_name,m.employee_id,m.last_name
from employees as e,employees as m
where e.`manager_id`=m.`employee_id`
```

#### sql 99 标准

##### 特点

1. 添加排序分组筛选
2. inner可以省略
3. 筛选条件放在where后面，连接条件放在了on的后面，提高了代码的可读性
4. inner join连接和sql92语法中的等值连接效果是一样的，都是查询交集部分

```mysql
语法：
    select 查询列表
    from 表1 别名
    join 表2 别名 
    on 连接条件
    where 筛选条件
    group by 分组
    having 筛选条件
    order by 排序列表

内连接（*****）:inner
外连接
    左外（*****）:left [outer]
    右外（*****）:right [outer]
    全外:full[outer]
交叉连接
```

##### 内连接

```mysql
select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件；
```

分类：

###### 等值连接

```mysql
#案例一：查询员工名，部门名
select last_name,department_name
from employees as e
inner join departments as d
on e.`department_id`=d.`department_id`;

#案例二：查询名字中包含e的员工名和工种名
select last_name,job_title
from employees as e
inner join jobs as j
on e.`job_id`=j.`job_id`
where e.`last_name` like '%e%';

#案例三：查询部门个数>3的城市名和部门个数（添加分组+筛选）,
select count(*),city
from departments as d
inner join locations as l
on d.`location_id`=l.`location_id`
group by city
having count(*)>3;

#案例四：查询哪个部门的员工个数>3的部门名和员工个数，并按个数排序（降序）。
select count(*),department_name
from employees as e
inner join departments as d
on e.`department_id`=d.`department_id`
group by department_name
having count(*)>3
order by count(*) desc;

#查询员工名，部门名，工种名，并按照部门名排序
select last_name,department_name,job_title
from employees as e
inner join departments as d on e.`department_id`=d.`department_id`
inner join jobs as j on e.`job_id`=j.`job_id`
order by department_name;

```

###### 非等值连接

###### 自连接



##### 外连接

###### 应用场景

用于查询一个表有，另一个表中没有的记录

###### 特点

1. 外连接的查询结果为主表中的所有记录
   - 如果从表中有和它匹配的，则显示匹配的值
   - 如果从表中没有和它匹配的，则显示null
   - 外连接查询结果=内连接结果+主表中有而从表没有的记录

2. 左外连接：left join左边是主表

   右外连接：right join右边是主表

3. 左外和右外交换两个表的顺序可以实现同样的效果

###### 应用

左（右）外连接

```mysql
#案例一：查询男朋友不在男神表的女神名（左外连接）
select b.name,bo.*
from beauty b
left outer join boys bo
on b.boyfriend_id=bo.id
where bo.id is null;
```

```mysql
# 查询哪个部门没有员工
use myemployees;
select d.*,e.employee_id
from departments as d
left outer join employees as e
on d.department_id=e.department_id
where e.employee_id is null;
```

全外连接

展现出左右外连接的所有部分

##### 交叉连接

说明：说白了就是一个笛卡尔乘积

```mysql
use girls;
select b.*,bo.*
from beauty as b
cross join boys as bo;
```

### sql 92 & sql 99 PK









​    





