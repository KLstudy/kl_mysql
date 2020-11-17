# MySQL

## 初识MySQL

### MySQL 服务的启动与停止

``` net stop <mysqlname> ``` 服务的停止

``` net start <mysqlname> ``` 服务的开启

### MySQL 的启动

说明：正常情况下不会在命令行启动，一般会在图形化界面使用，所以这里的指令，只做了解。

``` mysql -h localhost -p 3306 -u root -p ``` 回车输入密码。（ps:这里的-h和-p在本地是可以省略不写的）

``` exit ``` 退出MySQL

### MySQL 的常见命令

``` show databases;```  显示所有的数据库  

``` use <库名>; ``` 进入某个数据库  

``` show tables; ``` 显示该数据库中所有的表  

``` show tables from <库名>; ``` 显示所指定库中的表  

``` select database();``` 

``` mysql
creat table <表名>（
    列名 列类型，
    列名 列类型，
    ...
）
```



``` desc <表名>```  查看表结构



# DQL 语言





## 基础查询

``` mysql
select <要查询对象列表以逗号隔开> from <查询对象所在的表>；
```

## 条件查询

```mysql
select
<要查询的对象列表以逗号隔开>
from
<查询对象所在的表>
where
<查询条件>；
```

### 条件表达式

```mysql
where
salary>10000 #条件：工资大于一万
常用的条件运算符：> = < >= <= != <>(不等于)
```

### 逻辑表达式

```mysql
where
salary>10000 && salary<20000 #条件：工资大于一万且小于两万
常用的逻辑运算符：and(&&)  or(||) not(!)
```

### 模糊查询

```mysql
where
last_name like '%a%' #条件：表示名字中含有字母a的人，%表示通配符
```



## 排序查询

```mysql
select
<要查询的对象列表以逗号隔开>
from
<查询对象所在的表名>
where
<筛选条件>
order by
<排序方式>；
```

```mysql
order by
salary desc;#排序方式：降序排序。在排序查询中默认的排序方式是升序排序的
```

## 常见函数

### 字符函数

```mysql
concat拼接字符串
select concat(last_name,'_',first_name) from employees;

substr截取子串
select substr('helloworld',3)#返回lloworld，也就是说，只给出一个参数的时候，返回从此截断的子串
select substr('helloworld',3,6)#返回llowor,在这里第二个参数表示的是截取的步长（字符长度）

upper转换成大写
select upper('john')#返回JOHN

lower转换成小写
select lowwer('JOhn')#返回john

trim去前后指定的空格和字符
ltrim去左边空格
rtrim去右边空格
select trim('     hello     ');#返回hello

replace替换
select replace('hello','e','o');#返回hollo

lpad左填充
select lpad('hello',10,'+');#返回'+++++hello'

rpad右填充
select rpad('hello',10,'+');#返回'hello+++++'

instr返回子串第一次出现的索引
select instr('bbabababa')# 返回3

length 获取字节个数，注意是字节不是字符
select length('john') # 返回4
```

### 数学函数

```mysql
round 四舍无如
select round(1.6567,2);#返回1.66

rand 随机数，函数调用可以在0和1之间产生一个随机数
select rand(2);#返回：返回一个随即数，这里的2作为随机数种子，也就是说多次重复执行该函数，会出现重复的数字

ceil 向上取整
select ceil(1.0001);#返回2

floor 向下取整
select floor(1.9999);#返回1

truncate 截断
select truncate（1.699，2） #返回1.69

mod 取余
mod(a,b)  :   a-a/b*b
select mod(10,3) #返回1
```

### 日期函数

```mysql
now当前系统日期+时间
select now();#返回当前日期时间
select year(now());#返回当前年份

curdate当前系统日期
select curdate;#返回当前日期

curtime当前系统时间
select curtime;#返回当前时间

str_to_date 将字符转换成日期
select str_to_date('11-16-2020','%c-%d-%Y');#返回'2020-11-16'

date_format将日期转换成字符
select date_format('2019/12/12','%Y年%c月%d日');#返回：'2019年12月12日'

```

### 流程控制函数

```mysql
if（条件表达式，语句一，语句二）;如果条件表达式成立则执行语句一，不成立则执行语句二。
select if(10>5,'大','小')；#返回大

case语句 处理多分支
	when 常量一 then 要显示的值一（不加分号）或语句一（加分号）；
	when 常量二 then 要显示的值二（不加分号）或语句二（加分号）；
	...
	else 要显示的直n或者语句n
end
```

### 其他函数

```mysql
select version(); #版本
select database(); #当前库
select user(); #当前连接用户
```

## 分组函数

概述：用作统计使用

### 简单使用

```mysql
	sum 求和
	max 最大值
	min 最小值
	avg 平均值
	count 计数

	特点：
	1、以上五个分组函数都忽略null值，除了count(*)
	2、sum和avg一般用于处理数值型
		max、min、count可以处理任何数据类型
    3、都可以搭配distinct使用，用于统计去重后的结果(作为一个参数)
	4、count的参数可以支持：
		字段、*、常量值，一般放1

	   建议使用 count(*)
```

#### count()函数的使用

``` select count(salary) from employees;``` 

``` select count(*) from employees;``` #使用count(*)来统计整个表里面的对象数量（行数）

``` select count(1) from employees;``` #相当于在所查询的表前面加一行1，然后统计1的个数。其实说白了就是，加个常量值。

#### 和分组函数一同查询的字段

要求：必须是group by 后的字段

## 分组查询

### group by 语法



```mysql
select column,group_function(column)
from table
where condition
group by group_by_expression
order by column;
```

注意：查询列表比较特殊，要求是分组函数和group by后出现的字段

### 分组查询的使用

#### 普通筛选分组查询

```mysql
案例：查询邮箱中包含a字符的，每个部门的平均工资，按照平均工资从低到高排序
select avg(salary) as avgsalary,department_id
from employees
where email like '%a%'
group by department_id
order by avgsalary;
```

#### 按函数分组

```mysql
案例：员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些alter
#一，查询每个长度的员工个数
select count(*),length(last_name)
from employees
group by length(last_name)
#二，添加筛选条件
having count(*)>5;
```

#### 按多个字段分组

```mysql
案例：查询每个部门每个工种的平均工资
select avg(salary),department_id,job_id
from employees
group by department_id,job_id;#这里的两个参数的顺序是可以颠倒的
```





