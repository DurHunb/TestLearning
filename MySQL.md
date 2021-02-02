# 一、数据库相关概念

- **DB**：数据库（database），存储数据的仓库
- **DBMS**：数据库管理系统（Database Management System）。数据库是通过DBMS创建创建和操作的容器。
- **SQL**：结构化查询语言（Structure Query Language）。用来和数据库通信的语言
	- 不是某个特定数据库供应商专有的语言，几乎所有DBMS都支持SQL



#### 语法规范

1. 不区分大小写，但建议关键字大写

2. 每条命令最好以分号结尾

3. 每条命令根据需要，可以进行 缩进 或 换行 

4. 注释

	```mysql
	#单行注释
	
	-- 单行注释(注意有空格)
	
	/*
		多行注释
	*/
	```




#### 表相关sql

```mysql
#显示表departments的结构
desc departments;
```









## 1、SQL语言的分类



### 数据查询语言DQL

```mysql
select [查询列表] from [表名] join [表名] on [连接条件]
where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit offset,size
```



### 数据操纵语言DML

数据操纵语言DML主要有三种形式：

- 插入：INSERT

	```mysql
	INSERT INTO table_name (column1,column2,column3,...)
	VALUES (value1,value2,value3,...);
	```

- 更新：UPDATE

	```mysql
	UPDATE table_name
	SET column1=value1,column2=value2,...
	WHERE some_column=some_value;
	```

- 删除：DELETE

	```mysql
	DELETE FROM table_name
	WHERE some_column=some_value;
	```

	

### 数据定义语言DDL

数据定义语言DDL用来创建数据库中的各种对象-----表、视图、索引、同义词、聚簇等如：

> CREATE TABLE / VIEW / INDEX / SYN / CLUSTER
> 					|          |            |          |             |
>                    表       视图      索引   同义词       簇

DDL操作是隐性提交的！不能rollback 



### 数据控制语言DCL

数据控制语言DCL用来授予或回收访问数据库的某种特权，并控制
数据库操纵事务发生的时间及效果，对数据库实行监视等。如：

> 1) GRANT：授权。



> 2) ROLLBACK	 [WORK] 	TO 	[SAVEPOINT]：回退到某一点。
> 回滚---ROLLBACK
> 回滚命令使数据库状态回到上次最后提交的状态。其格式为：
> SQL>ROLLBACK;



> 3) COMMIT	 [WORK]	：提交。



## 2、提交的分类

  在数据库的插入、删除和修改操作时，只有当事务在提交到数据
库时才算完成。在事务提交前，只有操作数据库的这个人才能有权看
到所做的事情，别人只有在最后提交完成后才可以看到。
提交数据有三种类型：显式提交、隐式提交及自动提交。下面分
别说明这三种类型。


(1) 显式提交
用COMMIT命令直接完成的提交为显式提交。其格式为：
SQL>COMMIT；


(2) 隐式提交
用SQL命令间接完成的提交为隐式提交。这些命令是：
ALTER，AUDIT，COMMENT，CONNECT，CREATE，DISCONNECT，DROP，
EXIT，GRANT，NOAUDIT，QUIT，REVOKE，RENAME。


(3) 自动提交
若把AUTOCOMMIT设置为ON，则在插入、修改、删除语句执行后，
系统将自动进行提交，这就是自动提交。其格式为：
SQL>SET AUTOCOMMIT ON；



# 二、MySQL常用命令



- 本计算机启动/停止MySQL服务

```mysql
net start MySQL80
net stop MySQL80
```

- 查看服务器版本

```mysql
select version();
```

- 通过Windows自带的客户端登陆/推出MySQL

```mysql
#登录
mysql -h 主机名 -P 端口号 -u 用户名 -p 密码
#退出
exit 或 Ctrl+c
```

- 查看当前所有数据库

```mysql
show database;
```

- 打开指定的库

```mysql
use 库名;
```

- 查看当前库的所有的表

```mysql
show tables;
```

- 查看其他库的所有表

```mysql
show tables from 库名;
```

- 创建表

```mysql
create table 表名{
	列名 列类型,
	列名 列类型
}
```

- 查看表结构

```mysql
desc 表名;
```





# 三、Data Query Language



## 1、基础查询

```mysql
#1.查询列表可以是：表中的字段、常量值、表达式、函数
use [库名];
SELECT [查询列表] FROM [表名]

#2.查询常量值,(查询个寂寞?)
select 100;
select 'john';

#3.查询表达式，(MySQL版计算器?)
select 100*98;

#4.查询函数，(执行函数)
#返回mysql的版本
select version();
```



注意点：

1. 在最前面一定要加`use [库名]`，表示在那个表查询

2. 如果字段是关键字，则需要用着重号``表示是一个字段

	```mysql
	比如NAME是关键字，则查询的时候需要加着重号，表示是一个字段
	select `name` from test;
	```



#### 起别名

`where`后面不支持别名

`group by`，`having`，`order by`后支持别名

```mysql
#1.使用as
select 100%98 as 结果;
select last_name as 姓, first_name as 名 from employees;

#2.使用空格
select last_name 姓, first_name 名 from employees;

#当别名存在关键字时，用双引号/单引号
select salary as "out put" from employees;
```



#### `distinct`

当`distinct`应用到多个字段的时候，其应用的范围是其后面的所有字段，而不只是紧挨着它的一个字段，

而且`distinct`只能放到所有字段的前面

```mysql
select distinct apartment_id  from employees;
```



#### `+`的作用

Java中的+号

- 运算符，两个操作数都为数值型
- 连接符，只要有一个操作数为字符串

MySQL中的+号：

- 运算符

	```mysql
	#1.若两个操作都为数值型，做加法运算
	select 90+100;#190
	
	#2.若其中一方为字符型，试图将字符型数值转换为数值型，
	#如果转换成功，则继续做加法运算
	select '123'+90;#213
	#如果转换失败，则将字符型数值转为0
	select 'John'+90;#90
	
	#3.只要其中一方为null，则结果为null
	select null+10;#NULL
	```

	

#### 筛选符号



```mysql
select [查询列表] from [表名] where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit
```



- 按条件表达式筛选

	```mysql 
	>	<	=	!=	<>	>=	<=
	```

- 按逻辑运算符

	```mysql
	&&		||		!
	and		or		not
	```



## 2、模糊查询



**(1)`like`**

**通配符**

- `%`，任意多个字符，包含0个字符
- `_`，任意单个字符 

```mysql
#查询员工名中包含字符a的员工信息
select * from employees where last_name like '%a%';

#查询员工名中，第三个字符为e，第五个字符为a的员工名
select last_name from employees where last_name like '__e_a%';

#查询员工名中，第二个字符为_的员工名
#转义方式一：使转义字符
select last_name from employees where last_name like '_\_%';
#转义方式二：使用escape
select last_name from employees where last_name like '_$_%' escape '$';
```





(2)`between and`

- 包含临界值，相当于`<=`，`>=`
- 临界值顺序不能调换，不能`between 200 and 100`

```mysql
#查询员工编号在100到200之间的员工信息
select * from employees where employee_id between 100 and 200;

#查询员工编号不在100到200之间的员工信息
select * from employees where employee_id not between 100 and 200;
```



(3)`in `

- 判断某字段的值是否属于列表中的某一项，相当于`=`

- 不能使用通配符，不能`in('IT_PROG','AD_%')`

```mysql
#查询员工的工种编号是 IT_PROG,AD_VP,AD_PRES的员工名
select job_id,last_name 
from employees 
where job_id in ('IT_PROG','AD_VP','AD_PRES');

#查询员工的工种编号不是 IT_PROG,AD_VP,AD_PRES的员工名
select job_id,last_name 
from employees 
where job_id not in ('IT_PROG','AD_VP','AD_PRES');
```



(4)`is null`

- `bonus = null`的写法是错误的

```mysql
#查询没有奖金的员工名和奖金率
select last_name,bonus_rate from employees where bonus_rate is null;

#查询有奖金的员工名和奖金率
select last_name,bonus_rate from employees where bonus_rate is not null;
```





## 3、排序查询



- `asc`——升序，默认为升序
- `desc`——降序

```mysql
select [查询列表] from [表名] join [表名] on [连接条件]
where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit offset,size
```



```mysql
#1.查询员工信息，要求工资从高到低排序
SELECT * FROM employees ORDER BY salary DESC;

#2.按年薪的高低显示员工信息和年薪【按表达式排序】
SELECT *,salary*12*(1+IFNULL(bonus_rate,0)) AS 年薪 FROM employees 
ORDER BY salary*12*(1+IFNULL(bonus_rate,0)) DESC;

#2.按年薪的高低显示员工信息和年薪【按别名排序】
SELECT *,salary*12*(1+IFNULL(bonus_rate,0)) AS 年薪 FROM employees 
ORDER BY 年薪 DESC;

#3.按姓名长度显示员工的姓名和工资【按函数排序】
SELECT LENGTH(last_name),last_name,salary FROM employees 
ORDER BY LENGTH(last_name) DESC;

#4.查询员工信息，要求先按工资升序，再按员工编号降序【多个字段排序】
SELECT * FROM employees 
ORDER BY salary ASC,employee_id DESC
```

















##  4、常见函数



```mysql
# 案例，查询奖金率，如果为NULL，则为0
select ifnull(bonus_rate,0) as 奖金率 from employees;
```



### (1)单行函数

#### 字符函数

```mysql
#1、length 获取参数值的字节个数	(不是字符个数)
select length('john');#4
select length('张三封ha');#11,默认为utf-8，一个中文字符3个字节

#2、concat	拼接字符串
select concat(lastname,'_',firstname) as 姓名 from employees;

#3、upper——大写、lower——小写
select upper('joHn');#JOHN
select lower('JOHn');#john

#4、substr、substring——截取从某个索引之后的字符串
#注意：索引从1开始
select substr('李莫愁爱上了陆湛远',7) as output;#陆湛远
#注意，使用两个参数时，截取从指定索引处开始，指定字符长度
select substr('李莫愁爱上了陆湛远',1，3) as output;#李莫愁

#5、instr——返回字串第一次出现的索引，如果找不到返回0
select instr('杨不悔捡起了芝麻','杨不悔') as output ;#1

#6、trim——去除左右空格
select trim('   居怡娜   ') as output;#居怡娜
#去除左右指定字串
select trim('a' from 'aaaaaaa居aa怡娜aaaaaaaaaa') as output;#居aa怡娜

#7、lpad——用指定字符实现，左填充指定长度
select LPAD('居怡娜',10,'*') as output;#*******居怡娜
#当指定长度过小时
select LPAD('居怡娜',2,'*') as output;#居怡

#8、rpad——用指定字符实现，右填充指定长度
select rapd('居怡娜',10,'*') as output;#居怡娜*******

#9、replace	替换，如果存在多个相同的，那么都会替换
select replace('张无忌爱上了周芷若','周芷若','赵敏') as output;#张无忌爱上了赵敏

```



#### 数学函数

```mysql
#1、round 四舍五入（绝对值的四舍五入）
select round(1.55);#2
select round(-1.55);#-2
#小数点后保留
select round(1.655,2);#1.66

#2、ceil 向上取整,返回>=该参数的最小整数
select ceil(1.005);#2
select ceil(1.00);#1
select ceil(-1.02);#-1

#3、floor 向下取整，返回<=该参数的最大整数
select floor(5.2);#5
select floor(-9.9);#-10

#4、truncate 截断，后面不管几位都不保留
select truncate(1.6999,1);#1.6

#5、mod 取余 	mod(a,b)=a-a/b*b
select mod(10,3);#1
select 10%3;#1
#被除数为正，结果为正；被除数为负，结果为负
select mod(10,-3);#1
select mod(-10,3);#-1
```



#### 日期函数

```mysql
#1、now 返回当前系统日期+时间
select now();#2017-09-29 11:12:38

#2、curdate 返回当前系统日期，不包含时间
select curdate();#2017-09-29

#3、curtime 返回当前时间，不包含系统日期
select curtime();#11:12:38

#4、获得指定部分，年、月、日、小时、分钟、秒
select year(now()) as 年;
select year('1998-1-1') as 年;5
select year(hiredate) as 年 from employees;

#5、str_to_date:将字符通过指定格式，转化成日期
#查询入职时间为1999年9月13日的员工信息
select * from employees where hiredate = str_to_date('9-13 1999','%m-%d %Y') as output;

#6、date_format:将日期转换成，指定格式字符串
select date_format(now(),'%y年%m月%d日') as output;
#查询有奖金的员工名和入职日期（xx月/xx日 xx年）
select last_name,date_format(hiredate,'%m月/%d日/ %Y年')
from employees
where bonus_rate is not NULL;

```



| 序号 | 格式符 | 功能                     |
| ---- | ------ | ------------------------ |
| 1    | %Y     | 四位的年份               |
| 2    | %y     | 两位的年份               |
| 3    | %m     | 月份（01、02 ...11、12） |
| 4    | %c     | 月份（1、2 ...11、12）   |
| 5    | %d     | 日（01、02 ...）         |
| 6    | %H     | 小时（24小时制）         |
| 7    | %h     | 小时（12小时制）         |
| 8    | %i     | 分钟（00，01...）        |
| 9    | %s     | 秒（00，01...）          |



#### 其他函数

```mysql
select version();
select database();
select user();
```











### (2)分组函数

功能：做统计使用。又称为统计函数、聚合函数、组函数

```mysql
sum		求和
avg		平均值
max		最大值
min		最小值
count	计算非空值个数
```

- 特点

	- `sum`、`avg`一般用于处理数值型

		`max`、`min`、`count`可以处理任何类型

	- 所有的分组函数都忽略`NULL`值

	- 都可以和`distinct`搭配



- 简单使用

	```mysql
	select 
		sum(salary) as 总和,
		avg(salary) as 平均值,
		max(salary) as 最大值,
		min(salary) as 最小值,
		count(salary) as 个数
	from employees;
	
	#和distinct搭配
	select sum(distinct salary),sum(salary) from employees;
	```

- `count`函数

	- **==count(*)常用于计算个数==**
	
	```mysql
	#统计出符合条件的记录数
	select count(*) from employees;
	select count(1) from employees;#其中，2、3、'任博'等常量值都可以
	
	select count(*),department_id from employees
	group by department_id;

	#效率
	#myisam引擎下，count(*)的效率高
	#innodb引擎下，count(*)和count(1)效率差不多，比count(字段)要高
	```
	
	





## 5、分组查询

> 在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与聚合函数一起使用。
>
> HAVING 子句可以让我们筛选分组后的各组数据。

```mysql
select [查询列表] from [表名] join [表名] on [连接条件]
where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit offset,size
```

分组查询分类

- 分组前筛选
	- 数据源是原始表
	- `group by`前，关键字为`where`
- 分组后筛选
	- 数据源是分组后的结果集
	- `group by`后，关键字为`having`
	- 分组函数做筛选条件，放在`having`子句



```mysql
#添加分组前的筛选（筛选条件原始表存在）
#1.查询每个工种的最高工资
select max(salary),job_id from employees 
where bonus_rate is not null
group by job_id;

#2.查询每个位置上个部门个数
select location_id ,count(*)
from departments
group by location_id;

#按多个字段分组
#案例，查询每个部门每个工种的员工的平均工资
select avg(salary),department_id,job_id
from employees
group by job_id,department_id;#同一个部门的同一个工种会成为一行记录
```



```mysql
#添加分组后的筛选（筛选条件原始表不存在）


#案例1：查询哪个部门的员工个数>2

#第1步 查询每个部门的员工个数
select count(*),department_id from employees
group by department_id;

#第2步 根据1的结果，查询那个部门的员工数大于2

/*错误，因为where后的字段在数据库中不存在
select count(*),department_id from employees
where count(*)>2
group by department_id
*/

select count(*),department_id from employees
group by department_id
having count(*)>2;



#案例2 查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资

#第1步 查询每个工种有奖金的员工的最高工资

select max(salary),jod_id from employees
where bonus_rate is not null
order by job_id;

#第2步，继续筛选，最高工资大于>12000
select max(salary),jod_id from employees
where bonus_rate is not null
order by job_id;
having max(salary)>12000;
```







## 6、连接查询



按功能分类

### (1)内连接

SQL99语法——内连接

```mysql
select [查询列表] from 表1 inner join 表2 on 连接条件
where [筛选条件]
```

- 等值连接

	> ①为表起别名——简洁美观。但是起别名后，查询里的字段则不能使用**原本表名**去做限制
	>
	> ②当同一个字段存在多个表里，查询时需用表名对字段做限制。

	```mysql
	#查询员工名和对应的部门名、部门id
	select e.'department_id',department_name,last_name 
	from employees as e inner join departments as d 
	on e.'department_id' = d.'department_id';
	
	/*若是三表连接
	select department_name,last_name 
	from employees as e 
	inner join department1 as d1 on e.'department_id' = d1.'department_id'
	inner join department2 as d2 on e.'department_id' = d2.'department_id';
	
	
	*/
	
	#查询每个城市的部门数
	select count(*) as 个数,city 
	from department as d inner join location as l 
	on d.'location_id' = l.'location_id'
	group by city;
	```

- 非等值连接

	```mysql
	#查询员工的工资和工资级别
	#job_grades表，储存的是工资级别ABCDEF的各级别工资范围
	select salary,grade_level from employees as e inner join job_grades as g
	on salary  between g.'lowest_sal' and g.'highest_sal';
	```

- 自连接

	```mysql
	#查找员工名和他的直属上级
	select employee_id,e.last_name,manager_id,m.last_name
	from employees as e inner join employees as m
	on e.employee_id = m.manager_id;
	```

	

### (2)外连接

SQL99语法——外连接

> 应用场景：用于查询一个表中有，另一个表中可能没有的记录

**特点**：

1. 外连接查询的是主表中所有的记录

	> 如果从表中有和他匹配的，则显示匹配的值
	>
	> 如果从表中没有和他匹配的，则显示NULL

2. 左外连接，`left join/left out join`         左边的是主表

	右外连接，`right join/right out join`     右边的是主表



- 左外连接

  ```mysql
  #查询女生表中，有男朋友的女生
  select g.'name',b.* 
  from girl as g left outer join boy as b on g.'boyfriend_id'=b.'id'
  where b.'id' is not null;#因为是表里存在的字段，且没有分组，所以不用having做筛选
  ```

- 右外连接

	```mysql
	#查询女生表中，有男朋友的女生
	select g.'name',b.* 
	from boy as b right outer join girl as g on g.'boyfriend_id'=b.'id'
	where b.'id' is not null;
	```

- 全外连接

> FULL OUTER JOIN 关键字结合了 LEFT JOIN 和 RIGHT JOIN 的结果。

```mysql
/*
FULL OUTER JOIN 关键字返回左表（Websites）和右表（access_log）中所有的行。

如果 "Websites" 表中的行在 "access_log" 中没有匹配，会列出这些行
如果 "access_log" 表中的行在 "Websites" 表中没有匹配，也会列出这些行。
*/
SELECT Websites.name, access_log.count, access_log.date
FROM Websites FULL OUTER JOIN access_log
ON Websites.id=access_log.site_id
ORDER BY access_log.count DESC;
```



### (3)交叉连接

SQL99语法——交叉连接

> 作笛卡尔乘积

```mysql
#girl表11条数据，boy表4条数据，查询结果是44条数据
select g.*,b.* 
from girl as g cross join boy as b ;
```







## 7、子查询

> 含义：出现在其他语句中的select语句，称为子查询或内查询
>
> 按结果集的行列数不同：
>
> - 标量子查询（结果集只有一行一列）
> - 列子查询（结果集只有一列多行）
> 	- in/not in    ——等于列表中的任意一个
> 	- any/some ——和子查询返回的某一个值比较
> 	- all              ——和子查询返回的所有值比较
> - 行子查询（结果集只有一行多列）
> - 表子查询（结果集为多行多列）

按子查询出现的位置：

- `select`后面

	- 仅支持标量子查询（结果集只有一行一列）

	```mysql
	#查询每个部门的员工个数
	select d.*,(
	    select count(*) from employees as e where e.department_id = d.department_id
	) as 个数
	from departments as d
	```

	

- `from`后面

	- 支持表子查询

		> 将子查询结果充当一张表，要求必须对该表起别名

	```mysql
	#查询每个部门的平均工资等级
	select ag_dep.* ,grade_level
	from(
	    select avg(salary) as ag,department_id from employees
	    group by department_id
	) as ag_dep
	inner join job_grades  on ag_dep.ag between lowest_sal and highest_sal;
	```

	

- `where`或`having`后面

	- 标量子查询

	```mysql
	#谁的工资比Abel高
	select * from employees 
	where salary>(select salary from employees where last_name='Abel');
	
	#查询最低工资比50号部门高的部门id和最低工资
	select min(salary),department_id from employees
	group by department_id
	having min(salary)>(select min(salary) from employees where department_id = 50);
	```

	- 列子查询

	```mysql
	#返回location_id是1400或1700的部门中的所有员工姓名
	select last_name from employees 
	where department_id in
	(
	    select distinct department_id from departments where location_id in (1400,1700)
	);
	
	#返回其他工种中比job_id为IT_PROG工种任意员工工资低的员工信息
	select * from employees
	where job_id !=IT_PROG and salary < 
	any(
	    select distinct salary from employees where job_id = IT_PROG
	) ;
	
	#返回其他工种中比job_id为IT_PROG工种所有员工工资低的员工信息
	select * from employees
	where job_id !=IT_PROG and salary < 
	all(
	    select distinct salary from employees where job_id = IT_PROG
	) ;
	```

	- 行子查询

	```mysql
	#查询员工编号最小并且工资最高的员工信息
	select * from employees
	where (employee_id,salary)=(
	    select min(employee_id),max(salary)) from employees
	    );
	```

- `exists`后面（相关子查询）

	- 支持表子查询

		> 语法：exist（完整的子查询）
		>
		> 结果：如果存在值，则返回1；不存在值，则返回0

	```mysql
	# 查询有员工的部门名
	select department_name from departments as d
	where exists(
	    select * from employees as e where d.department_id = e.department_id
	);
	#满足这个条件的看有没有
	#用子查询过滤主查询的结果
	```





## 8、分页查询

> 应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求时
>
> 
>
> SELECT [查询列表] FROM [表名] LIMIT (page-1)*size , size;
>
> ​			page：要显示的第几页
>
> ​			size：每页要显示的条目数



```mysql
limit
```



```mysql
select [查询列表] from [表名] join [表名] on [连接条件]
where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit offset,size
#offset:要显示条目的起始索引(起始索引从0开始)
#size:要显示的条目个数
```



```mysql
#查询前五条员工信息
select * from employees limit 0,5;
#如果索引从第一条开始，可以省略0
select * from employees limit 5;

#有奖金的员工信息，并且工资较高的前十名
select * from employees where bonus_rate is not null
order by salary desc
limit 10;
```







## 9、联合查询

> 将多条查询语句的结果合并为一个结果
>
> 应用场景：要查询的结果来自多个表，但这多个表中没有连接关系



```mysql
union

/*语法
		查询语句1
		union
		查询语句2
		union
		查询语句3
		...
*/
```

特点：

- 要求多条查询语句的查询列数是一致的
- 要求多条查询语句的查询结果的每一列意义相同，方便看
- `unioin`默认去重，如果不想去重，可以用 `union all`，查询结果包含重复项

```mysql
#查询部门编号>90 或 邮箱包含a 的员工信息
select * from employees where email like '%a%'
#union默认去重，如果不去重，可以用 union all
union
select * from employees where department_id > 90;

#等价于
select * from employees where email like '%a%' or department_id > 90;
```











# 四、Data Manage Language



## 1、插入语句



```mysql
#方式一
INSERT INTO 表名 (column1,column2,column3,...)
VALUES (value1,value2,value3,...);

#insert into beauty values(...)	#如果表名后不加字段，则默认为全部字段

#方式二
INSERT INTO 表名
SET 列名=值,列名=值,列名=值...
```

两种方式比较

- 方式一支持插入多行，方式二不支持

	```mysql
	insert into beauty(id,name,sex,borndate)
	values(13,'居怡娜','女','1998-04-28'),
	(14,'居一娜','女','1998-04-28'),
	(15,'居二娜','女','1998-04-28'),
	(16,'居三娜','女','1998-04-28'),
	```

- 方式一支持子查询，方式二不支持

	```mysql
	#把子查询的结果集，插入进去
	insert into beauty(id,name,phone)
	select 26,'居四娜','13889898989';#查询了三个常量，也可以查询表
	
	insert into beauty(id,name,phone)
	select id,boyname,'13889898989' from boys where id<3;
	```

	





## 2、修改语句



```mysql
#1、修改单表的记录
UPDATE 表名
SET column1=value1,column2=value2,...
WHERE some_column=some_value;

#2、修改多表的记录
UPDATE 表名1 INNER|LEFT|RIGHT JOIN 表名2 ON 连接条件
SET column1=value1,column2=value2,...
WHERE some_column=some_value;
```



```mysql
#修改单表记录
#修改beauty表中姓唐的电话为'13889898989'
update beauty set phone='13889898989' where name like '唐%';

#修改多表记录
#修改张无忌的女朋友的手机号为114
update boys as b1 inner join beauty as b2 on b1.id = b2.boyfriend_id
set b2.phone = '114'
where b1.name='张无忌';
```





## 3、删除语句



```mysql
#方式一：delete
#1、单表删除
DELETE FROM 表名
WHERE some_column=some_value;

#2、多表删除

#DELETE table_name1,table_name2 	#表示删除table_name1,table_name2的内容
DELETE 表名1 					#表示删除table_name1的内容
FROM 表名1 INNER|LEFT|RIGHT JOIN 表名2 ON 连接条件
SET column1=value1,column2=value2,...
WHERE some_column=some_value;



#方式二：truncate
truncate table 表名;#删除表里的全部行
```



两种方式比较：

- `delete `可以加 `where` 条件，`truncate`不能加

- `truncate`删除，效率高

- **假如要删除的表中有自增列，二者删除效果有差别**

	如果用`delete`删除，再插入数据，自增长列的值从断点开始

	如果用`truncate`删除，再插入数据，自增长列的值从1开始

- **删除的表时，二者返回值有差别**

	如果用`delete`删除，有返回值（xxx行受影响）

	如果用`truncate`删除，无返回值（0行受影响）

- **回滚**

	如果用`delete`删除，可以回滚

	如果用`truncate`删除，不能回滚



```mysql
#方式一

#单表删除
#删除手机号以9结尾的女生信息
delete from beauty where phone like '%9';

#多表删除
#删除张无忌的女朋友
#delete b1,b2 #这样的话，boy表里的张无忌也会被删除
delete b1 
from beauty as b1 inner join boy as b2 on b1.boyfriend_id=b2.id
where b2.name='张无忌';
```

```mysql
#方式二
#删除boy表的全部行
truncate table boys;
#delete from boys; #delete的删除表
```











# 五、Data Define Language

## 1、库的管理

- 库的创建

	```mysql
	CREATE DATABASE  [IF NOT EXISTS] 数据库名;
	```

- 库的修改

	```mysql
	#更改库的字符集
	alter database books character set gbk;
	```

- 库的删除

	```mysql
	DROP DATABASE [IF EXISTS] 数据库名
	```

	

```mysql
create database books;
create database if not exists books;#当库存在时，不会报错

drop database if exists books;#当库不存在时，不会报错

#[IF EXISTS]仅在库、表的创建和删除有用，表内字段删除时不能用
```







## 2、表的管理



#### 表的创建

```mysql
CREATE TABLE [IF NOT EXISTS] 表名(
	col_name1 col_type [(长度)约束],
    col_name2 col_type [(长度)约束],
    col_name3 col_type [(长度)约束],
    col_name4 col_type [(长度)约束]
);
```



```mysql
#创建表book
create table book{
	id int,#编号
	bname varchar(20),#图书名
	price double,#价格
	authorId int,#作者编号
	publishDate datetime#出版日期
};
#创建表author
create table author{
	id int,#编号
	name varchar(20),#名字
};
```



#### 表的修改

```mysql
ALTER TABLE 表名 CHANGE|MODIFY|ADD|RENAME TO|DROP COLUMN 列名 [列类型(约束)]
```



- 修改列名

```mysql
#把book表中的publishDate字段，改为pubDate
alter table book change column publishDate pubDate datetime;
```

- 修改类的类型或约束

```mysql
#把book表中的pubDate字段类型，改为timestamp
alter table book modify column pubDate timestamp;
```

- 添加新列

```mysql
#在author表添加新列 annual #年薪
alter table author add column annual double;
```

- 删除列

```mysql
#删除author表的 列 annual 
alter table author drop column annual;
```

- 修改表名

```mysql
#修改author表的表名为book_author
alter table author rename to book_author;
```



#### 表的删除

```mysql
DROP TABLE [IF EXISTS] 表名;
```



#### 表的复制

- 仅仅复制表的结构

```mysql
create table copy1 like book;
```

- 复制表的结构+数据

```mysql
create table copy2
select * from book;
```

- 只复制部分数据

```mysql
create table copy3
select id,bname  from book where price < 100;
```

- 仅仅复制部分结构

```mysql
create table copy4
select id,bname  from book where 1=2;		#设置成没有满足条件的数据，就不会有数据复制过去
```





## 3、数据类型



#### 整型

`tinyint`，`smallint`，`mediumint`，`int/integer`，`bigint`

  1字节	，   2字节	 ，    3字节	  ，		4字节	  ，  8字节

特点：

- 默认是有符号，如果想设置无符号，需要添加`unsigned`关键字

- 如果插入的数字超处了整型的范围，会报异常，并且插入的是临界值

- 如果不设置长度，会有默认的长度。长度仅代表显示的数字位数，与大小范围无关

	与`zerofill`搭配使用，加了`zerofill`后，默认会变为无符号

```mysql
#如何设置有符号和无符号
create tab_int(
	t1 int,				#有符号
    t2 int unsigned,	#无符号
    
    #加了zerofill后，默认会变为无符号。当数字不满足6位时，会从左边以0填充位数。满足则不填充
    t3 int(6) zerofill  
)
```



#### 小数

- 浮点型

	`float(M,D)`，`double(M,D)`

	​	4字节        ，       8字节

- 定点型

	`DEC(M,D)`/`DECIMAL(M,D)`

	最大取值范围与double相同，但是<font color='red'>精确度更高</font>，涉及货币运算等用decimal

	

**特点**：

- M：整数部位+小数部位的长度

  D：小数部位的长度

- M、D可以省略

  如果是decimal，则M默认为10，D默认为0

  如果是float/double，无限制（只要在相应的取值范围内）

```mysql
create tab_float(
	t1 float(5,2),				
    t2 float(5,2),	
    t3 decimal(5,2)
)
```

​	

#### 字符型

保存较短的文本：

​	`char`，`varchar`

​	`binary`，`varbinary`用于保存较短的二进制

​	`enum`用于保存枚举——enum(‘a’,’b’,’c’)

​	`set`用于保存集合——set(‘a’,’b’,’c’)

保存较长的文本：

​	`text`，`blob`

|         | 写法       | M的意思    | 特点       | 空间的耗费 | 效率 |
| ------- | ---------- | ---------- | ---------- | ---------- | ---- |
| char    | char(M)    | 最大字符数 | 固定的长度 | 比较耗费   | 高   |
| varchar | varchar(M) | 最大字符数 | 可变的长度 | 比较节省   | 低   |



#### 日期类型

`date`：只保存日期

`time`：只保存时间

`year`：只保存年

`datetime`：保存日期+时间，不受时区影响

- 8个字节
- 1000—9999

`timestamp`：保存日期+时间，受时区影响

- 4个字节
- 1970—2038





## 4、常见约束



```mysql
#查看表中所有的索引，包括主键、外键、唯一，以及自己建的索引
show index from 表名
```

六大约束

- **NOT NULL** 非空
- **PRIMARY KEY** 主键
- **UNIQUE** 唯一
- **CHECK** 检查约束,MySQL 不支持,语法不报错但无效果
- **FOREIGN KEY** 外键.限制两表关系,通常在从表引入外键约束,引入主表中某列的值,保证从表该字段的值必须来源于主表关联列的值
- **DEFAULT** 默认值/系统设定值



> **六大约束都可在列级约束使用,但外键无效果(外键是约束两个表之间的关系)**
>
> **显而易见,非空和默认不可在表级约束中使用**
>
> 一般外键采用表级约束，其余采用列级约束



```mysql
#1、添加列级约束
create table stuinfo(
	id int primary key,									#主键
    stuname varchar(20) not null,						#非空
    gender char(1) check(gender='男' or gender='女'),	   #检查
    seat int unique,									#唯一	
    age int default 18,									#默认约束
    majorid int foreign key references major(id)		#外键，不报错，但没效果
    /*
    class varchar(30) not null unique,#多条约束添加没有顺序要求
    */
    
);
create table major(
    id int primary key,
    majorname varchar(20)
);
```



```mysql
#2、添加表级约束
#非空和默认不支持表级约束
create table stuinfo(
	id int ,
    stuname varchar(20) not null,#非空
    gender char(1) ,
    seat int ,
    age int default 18,#默认约束
    majorid int,
    
    constraint name1 primary key(id，stuname),#可组合				         #主键
    constraint name2 unique(seat，age),	#可组合							 #唯一
    constraint name3 check(gender='男' or gender='女'),			#检查
    constraint fk_stuinfo_major foreign key(majorid) references major(id)	 #外键，表级约束成功添加
);
create table major(
    id int primary key,
    majorname varchar(20)
);
```



- 修改表时添加约束

```mysql
#添加非空约束
alter table stuinfo modify column stuname varchar(20) not null;
#添加默认约束
alter table stuinfo modify column age int default 18;

#添加主键
#①列级约束添加
alter table stuinfo modify column id int primary key;
#②表级约束添加(可加名字)
alter table stuinfo add [constraint name] primary key(id);

#添加唯一
#①列级约束添加
alter table stuinfo modify column seat int unique;
#②表级约束添加(可加名字)
alter table stuinfo add [constraint name] unique(seat);

#添加外键(可加名字)
alter table stuinfo add [constraint fk_stuinfo_major] foreign key(majorid) references major(id);
```



- 修改表时删除约束

```mysql
#删除非空约束
alter table stuinfo modify column stuname varchar(20)  null;
#删除默认约束
alter table stuinfo modify column age int ;

#删除主键
alter table stuinfo drop primary key;

#删除唯一
alter table stuinfo drop index name;#name是唯一索引的名字

#删除外键
alter table stuinfo drop foreign key name;#name是外键的名字
```









## 5、标识列

> 又称为自增列



```mysql
#查看auto_increment的参数
show variables like '%auto_increment%';
#设置步长=3	(可以通过手动在表中插入值，设置起始值)
set auto_increment_increment = 3
```



特点：

- 标识列不一定必须和主键搭配
- 一个表最多至多有一个标识列
- 标识列的类型只能是数值型



- 创建表时设置标识列

```mysql
create table if exists tab_identity(
	id int unique auto_increment,
    name varchar(20)
);
```

- 修改表时设置标识列

```mysql
alter table tab_identity modify column id int primary key auto_increment;
```









# 六、Transaction Control Language

事务控制语言



> 事务：由单独单元的一个或多个SQL语句组成，在这个单元中，每个SQL语句都是相互依赖的。
>
> ​			如果某一条语句一旦执行失误，整个单元都会回滚



**存储引擎**

>  概念：在Mysql中的数据用各种不同的技术存在文件\内存中

```mysql
#查看mysql支持的存储引擎
show engines;
```

在MySQL中的存储引擎有`innodb,myisam,memory`。

- 其中`innodb`支持事务，而`myisam,memory`不支持



### 事务的ACID属性

- 原子性（Atomicity）

	事务是一个不可分割的单位。事务的操作要么都发生，要么都不发生

- 一致性（Consistency）

  事务必须使数据库从一个一致性状态变换到另一个一致性状态。

  一致性解释：

  > A要向B支付100元,而A的账户中只有90元,并且我们给定账户余额这一列的约束是,不能小于0.那么很明显这条事务执行会失败,因为90-100=-10,小于我们给定的约束了.

  ​		这个例子里,支付之前我们数据库里的数据都是符合约束的,但是如果事务执行成功了,我们的数据库数据就破坏约束了,因此事务不能成功,这里我们说事务提供了一致性的保证.

  ​		然后我们再看个例子

  > A要向B支付100元,而A的账户中只有90元,我们的账户余额列没有任何约束.但是我们业务上不允许账户余额小于0.因此支付完成后我们会检查A的账户余额,发现余额小于0了,于是我们进行了事务的回滚.

  ​		这个例子里,如果事务执行成功,虽然没有破坏数据库的约束,但是破坏了我们应用层的约束.而事务的回滚保证了我们的约束,因此也可以说事务提供了一致性保证(ps:事实上,是我们应用层利用事务回滚保证了我们的约束不被破坏).

  ​		最后我们再看个例子

  > A要向B支付100元,而A的账户中只有90元,我们的账户余额列没有任何约束.然后支付成功了.

  ​		这里,如果按照很多人的理解,事务不是保证一致性么?直观上账户余额为什么能为负呢.但这里事务执行前和执行后,我们的系统没有任何的约束被破坏.一直都是保持正确的状态.

  ​		所以，综上，可以理解一致性就是:应用系统从一个正确的状态到另一个正确的状态.而ACID就是说事务能够通过AID来保证这个C的过程.C是目的,AID都是手段.

- 隔离性（Isolation）

	事务的隔离性是指一个事务的执行不能被其他事务干扰，即一个事物的内部操作和数据对并发的其他事务是隔离的。

	并发执行的各个事务之间不能互相干扰

- 持久性（Durability）

	持久性是指一个事务一旦被提交，它对数据库中数据的改变是永久性的。



### 事务的创建

**隐式事务**：比如`insert`、`update`、`delete`等语句，自动提交

```mysql
delete from department where id='1';
```



**显式事务**：

- 前提：必须设置自动提交功能为禁用

```mysql
#1、关闭自动提交，只针对当前会话有效
set autocommit = 0; 
#2、开启事务	#可选的，set autocommit = 0就默认开启
start transaction;

#3、编写事务的sql语句
select/ insert/ update/ delete

#4、结束事务
#Mysql没法进行当提交失败时回滚，只能在JDBC中才能进行
#当前只能要么提交事务，要么回滚事务。达不到根据情况进行
commit;#提交事务
rollback;#回滚事务
```



### 数据库并发问题

**脏读**：对于两个事务T1、T2。

​			T1读取了已经被T2更新，但是还没被提交的字段。

​			若T2回滚，T1读取的内容就是临时且无效的。

**不可重复读**：对于两个事务T1、T2。

​			T1读取了一个字段，然后T2<font color=red>更新</font>了该字段。

​			T1再次读取同一个字段，值不同了。

**幻读**：对于两个事务T1、T2。

​			T1从一个表读取了一个字段，然后T2在该表中<font color=red>插入</font>了新的行。

​			T1再次读取同一个表，就会多出几行。



#### 四种隔离级别

> 查看表的隔离级别
>
> ```mysql
> #查看隔离级别
> select @@tx_isolation;
> 
> #设置read uncommitted级别：
> set session transaction isolation level read uncommitted;
> ```



- Read Uncommitted——读未提交

	- 允许事务读取未被其他事务提交的变更

	- 脏读、不可重复读、幻读都会出现

- Read Committed——读已提交

	- 只允许事务读取已被其他事务提交的变更。
	- 可以避免脏读
	- 不可重复读、幻读都会出现

- Repeatable Read——可重复读

	- 确保事务可以多次从一个字段中读取相同的值。

		在这个事务的持续期间内，禁止其他事务对这个字段进行<font color='red'>更新</font>

	- 可以避免脏读、不可重复读

	- 幻读会出现

- serializable——串行化

	- 确保事务可以从一个表中读取相同的行。

		在这个事务的持续期间，禁止其他事务对该表执行插入、更新、删除操作

	- 所有并发问题都可以避免，但性能极其低下



### 回滚点

- `savepoint`的使用

```mysql
set  autocommit = 0;
start transaction;
delete from account where id=25;
savepoint a;#设置保存点
delete from account where id = 35;      
rollback to a;#回滚到保存点a
#	id=35的没删，id=25的删除
```





# 七、视图

> 视图中的字段就是来自一个或多个数据库中的真实的表中的字段。
>
> 创建视图时，可以使用 SQL 函数、WHERE 以及 JOIN 语句等



**注意：**

>  视图总是显示最近的数据。每当用户查询视图时，数据库引擎通过使用 SQL 语句来重建数据。

> 视图可以使用insert、update等，但有很严重的限制
>
> 并且因为会作用到原表，所以一般不允许更新视图

- 创建视图

  ```mysql
  create view myv1
  as
  select last_name,first_name from employees
  ```

- 使用视图

  ```mysql
  select * from myv1 where last_name like '%a%';
  ```

  - 视图还可以和表做`join`操作

- 修改视图

  - 方式一

  ```mysql
  create or replace view 视图名
  as	查询语句
  ```

  ```mysql
  create or replace view myv1
  as
  select id，age,sex from employees
  ```

  - 方式二

  ```mysql
  alter view 视图名
  as 查询语句
  ```

  ```mysql
  alter view myv1
  as
  select id，age,sex from employees
  ```

- 删除视图

  ```mysql
  drop view 视图名1,视图名2,视图名3
  ```

- 查看视图(查看视图结构)

  ```mysql
  #方式一
  desc 视图名;
  #方式二
  show create view 视图名;
  ```

  





# 八、变量

**1、系统变量**：变量由系统提供，不是用户定义

- 1.1	全局变量：服务器每次重启以后，都会把所有全局变量重置为初始值

  ​							针对所有会话（连接）有效

- 1.2    会话变量：针对当前会话（连接）有效

```mysql
#1、查看所有的系统变量
show variables;
	#1.1、查看所有全局变量
	show global variabs;
	#1.2、查看所有会话变量s
	show session variables;
	
#2、查看包含xxx的系统变量
show global variabless like '%xxx%';
show session variabless like '%xxx%';

#3、查看指定的某个系统变量的值
select @@global.系统变量名
select @@session.系统变量名

#3、为某个系统变量赋值
	#方式一
	set global 系统变量名 = 值;
	set session 系统变量名 = 值;
	#方式二
	set @@global.系统变量名 = 值;
	set @@session.系统变量名 = 值;
```





**2、自定义变量**

- 2.1	用户变量：针对当前会话（连接）有效

```mysql
#1、声明并初始化
	#方式一：
	set @用户变量名=值;
	#方式二：
	set @用户变量名:=值;
	#方式三：
	select @用户变量名:=值;
	
#2、赋值
	#方式一：
	set @用户变量名=值;
	#方式二：
	set @用户变量名:=值;
	#方式三：
	select @用户变量名:=值;
	#方式四：
	select 字段 into 变量名 from 表;
	例：select count(*) into @count from employees;
	
#3、查看
	select @count;
```



- 2.2	局部变量：仅仅在定义它的`begin-end`中有效

  ​							应用在`begin-end`中第一句话

```mysql
#1、声明并初始化
	#方式一：
	declare 变量名 类型;
	declare 变量名 类型 default 值;
	
#2、赋值
	#方式一：
	set @局部变量名=值;
	#方式二：
	set @局部变量名:=值;
	#方式三：
	select @局部变量名:=值;
	#方式四：
	select 字段 into 局部变量名 from 表;
	例：select count(*) into @count from employees;
	
#3、查看
	select @count;
```





# 九、存储过程

> 一组预先编译好的SQL语句的集合，可以理解成批处理语句
>
> - 提高代码的重用性
> - 减少了编译次数并且减少了和数据库服务器的连接次数，提高效率



**创建语法**

```mysql
delimiter 重新设定的结束符;
create procedure 存储过程名(参数模式 参数名 参数类型)
begin
		存储过程体(一组有效的SQL语句)
end 重新设定的结束符
```

**注意：**

- 参数模式：
  - in：该参数作为传入值
  - out：该参数可以作为返回值
  - inout：该参数既可以作为传入值，也可以作为返回值

- 存储过程体中的每条SQL语句的结尾，必须加分号

- 存储过程体结尾，需要用`delimiter`重新设定结束符（本次会话有效）

  默认情况下，delimiter是分号



**调用语法**

```mysql
call 存储过程名(参数列表)  
```

**删除语法**

```mysql
drop procedure 存储过程名;
#drop procedure 存储过程名1,存储过程名2; #报错，不支持
```

**查看语法**

```mysql
#desc myp1; #会提示不存在，这是因为desc只能查看表
show create procedure myp1;
```






**例子**

1、空参列表

```mysql
#创建
delimiter $;
create procedure myp1()
begin
	insert into admin(id,username) 
	values('1',john),('2',Tom),('3',jerry);
end $

#调用
call myp1()$
```

2、带in模式参数

```mysql
#创建存储过程实现，用户是否登陆成功
delimiter $;
create procedure myp2(in username varchar(20),in password varchar(20))

begin

	#声明result
	declare result varchar(20) default '' ; 
	
	#把count(*)赋值给result
	select count(*) into result 
	from admin 
	where admin.username=username and admin.password=password;
	
	#使用result,相当于打印结果
	select if(result>0,'成功','失败') 
	
end $

#调用
call myp2('居依娜'，'123456')$
```

3、带out模式参数

> 不用加return语句，out参数赋值后，会自动返回

```mysql
#创建存储过程实现，统计重名的个数
delimiter $;
create procedure myp3(in username varchar(20),out num int)

begin	
	
	select count(*) into num
	from admin 
	where admin.username=username;
		
end $

#调用
set @num $ 
call myp3('居依娜',@num)$			#也可以不定义@num，直接call myp3('居依娜',@num)$
select @num $
```

4、带inout模式参数

```mysql
#传入a,b的值，并返回a,b翻倍的值a
delimiter $;
create procedure myp4(inout a int,inout b int)

begin	
	
	set a=a*2;
	set b=b*2;
    
end $

#调用
set @m = 10 $ 
set @n = 20 $ 
call myp4(@m,@n)$	
select @m,@n $
```













# 十、函数

> 函数跟存储过程类似，也是一组sql语句。



区别：

 - 存储过程可以有0个返回，也可以有多个返回。

   比较适合做批量增删改查

 - 函数有且仅有一个返回

   比较适合处理数据后返回一个结果



**创建语法：**

```mysql
delimiter 重新设定的结束符;
create function 函数名(参数名 参数类型) returns 返回类型
begin
	函数体(一组有效的SQL语句)
	return 值;
end 重新设定的结束符
```

- 函数体中的每条SQL语句的结尾，必须加分号；

- 函数体结尾，需要用`delimiter`重新设定结束符（本次会话有效）

  默认情况下，delimiter是分号

**调用语法**

```mysql
select 函数名(参数名)
```

**查看函数**

```mysql
show create function myf2;
```

**删除函数**

```mysql
drop function myf2;
```





**例子：**

1、无参数有返回

```mysql
#创建
delimiter $;
create function myf1() returns int
begin 
	declare c int default 0;
    select count(*) into c
    from employees;
    return c;
end $

#调用
select myf1()$
```

2、有参有返回

```mysql
#根据员工名返回他的工资
delimiter $;
create function myf2(empName varchar(20)) returns double
begin

	#定义用户变量
	set @sal=0;
	
	select salary into @sal
	from employees
	where lastname = empName;
	return @sal;
end $

#调用
select myf2('k_king') $

```





# 十一、流程控制



## 分支函数

- `if`函数

```mysql
#1、if函数
select if(10<5,'大','小');
select last_name,bonus_rate,if(bonus_rate is null,'没奖金','有奖金') as 备注
from employees

```

- case函数

  > 注意两种使用方式的区别，以及结构区别

```mysql

#使用一：switch case 的效果，一般用于等值判断

/*案例：查询员工工资，要求
部门号=30，显示的工资为1.1倍
部门号=40，显示的工资为1.2倍
部门号=50，显示的工资为1.3倍
其他部门，显示的工资为原工资
*/
#case相当于一个表达式
select salary as 原始工资,department_id,
case department			#区别：该行加了【字段/表达式】
when 30 then salary*1.1	#then 后面只能是值
when 40 then salary*1.2
when 50 then salary*1.3
else salary
end 
as 新工资
from employees;	


#使用二：类似多重if,一般用于区间判断

/*案例：查询员工工资，要求
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000，显示C级别
否则显示D级别
*/
select salary,
case					#区别：该行没加【字段/表达式】
when salary>20000 then 'A'	#then 后面只能是值
when salary>15000 then 'B'
when salary>10000 then 'C'
else 'D'
end
as 工资级别
from employees;	
```





## 分支结构

> 只能在begin-end中使用
>
> 注意使用方法与流程控制函数区分

`if`结构

```mysql
#案例：根据传入的成绩显示等级
delimiter $;
create function test1(score int) returns char
begin

	if score>=90 and score<=100 then return 'A';
	elseif score>=80 and score<=100 then return 'B';
	elseif score>=60 and score<=100 then return 'C';
	else return D;
	end if;
	
end $
```



`case`结构

```mysql
#案例：根据传入的成绩显示等级
delimiter $;
create procedure test2(in score int) 
begin

	case
	when score>=90 and score<=100 then select 'A'; #then 后面只能是语句
	when score>=80 and score<=100 then select 'B';
	when score>=60 and score<=100 then select 'C';
	else select D;
	end case;
	
end $
```





## 循环结构










# EX-1 转义

```mysql
#查询员工名中，第二个字符为_的员工名
#转义方式一：使转义字符
select last_name from employees where last_name like '_\_%';
#转义方式二：使用escape
select last_name from employees where last_name like '_$_%' escape '$';
```





# EX-2 安全等于



- 安全等于，进行的是等于操作
- 既可以判断`null`值，也可以判断普通的数值

```mysql
#查询没有奖金的员工名和奖金率
select last_name,bonus_rate from employees where bonus_rate <=> null;

#查询工资为12000的员工信息
select * from employees where salary <=>12000;
```







# EX-3 ANY/SOME/ALL

> **准备两个表：**
>
> --T1(2,3)
> --T2(1,2,3,4)

**ALL 父查询中的结果集大于子查询中每一个结果集中的值,则为真**

```mysql
SELECT * FROM T2 WHERE N>ALL (SELECT N FROM T1)#4
```



**ANY,SOME 父查询中的结果集大于子查询中任意一个结果集中的值,则为真**

```mysql
SELECT \* FROM T2 WHERE N>ANY(SELECT N FROM T1)#3、4
SELECT \* FROM T2 WHERE N>SOME(SELECT N FROM T1)#3、4
```



















# 面试题



```mysql
#1、判断以下两种方式执行结果是否一样，并说明原因
select * from employees;
select * from employees where last_name like '%%' and bonus_rate like '%%';

#不一样,如果判断的字段中有NULL值

```



```mysql
#2、查询语句中涉及到的关键字的执行顺序
select [查询列表] from [表名] join [表名] on [连接条件]
where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit offset,size

1、from
2、join
3、on
4、where
5、group by
6、having
7、select
8、order by
9、limit
#每一条子句都会生成一个虚拟表格
```



```mysql
#3、truncate和delete的比较
（1）表的部分区别，见删除语句部分

（2）`delete`和`truncate`在事务使用时的区别
#演示delete
set autocommit = 0;
start transaction;
delete from account;
rollback;

#演示truncate
set autocommit = 0;
start transaction;
truncate table account;
rollback;

#delete 支持回滚
#truncate 已经提交了磁盘文件，不支持回滚
```



4、主键和唯一的对比

|             | 数值唯一性 | 值是否可为空 | 一个表中可以有几个 | 是否可以组合 |
| ----------- | ---------- | ------------ | ------------------ | ------------ |
| primary key | √          | ×            | 至多一个           | 可以         |
| unique      | √          | √            | 可以有多个         | 可以         |





