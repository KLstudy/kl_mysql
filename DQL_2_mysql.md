# MySQL

## 连接查询

概述：又称为多表查询，当查询的字段来自多个表时，就会用到连接查询

#### 笛卡尔乘积现象

概述：假设表1有m行，表2有n行，结果等于m*n行

原因：没有有效的连接条件

```mysql
select count(*) from beauty;
这里输出12行
select count(*) from boys;
这里输出4行
select name,boyname from boys,beauty
那么最终结果就是48行
```

```mysql
select count(*) from beauty;
select count(*) from boys;
select name,boyname from boys,beauty
where beauty.boyfrinend_id=boys.id;
这里的就正常了
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

##### 等值连接

```mysql
select name,boyname from boys,beauty
where beauty.boyfrinend_id=boys.id;
#这就是一个等值连接
```

```mysql
案例：查询员工名对应的部门名
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







