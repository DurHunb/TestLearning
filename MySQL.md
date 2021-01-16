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
#显示表结构departments的结构
desc departments
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



#### 流程控制函数

- `if`函数

```mysql
#1、if函数
select if(10<5,'大','小');
select last_name,bonus_rate,if(bonus_rate is null,'没奖金','有奖金') as 备注
from employees

```

- case结构（注意两种使用方式的区别，以及结构区别）

```mysql

#使用一：switch case 的效果

/*案例：查询员工工资，要求
部门号=30，显示的工资为1.1倍
部门号=40，显示的工资为1.2倍
部门号=50，显示的工资为1.3倍
其他部门，显示的工资为原工资
*/
#case相当于一个表达式
select salary as 原始工资,department_id,
case department			#区别：该行加了【字段/表达式】
when 30 then salary*1.1
when 40 then salary*1.2
when 50 then salary*1.3
else salary
end 
as 新工资
from employees;	


#使用二：类似多重if

/*案例：查询员工工资，要求
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000，显示C级别
否则显示D级别
*/
select salary,
case					#区别：该行没加【字段/表达式】
when salary>20000 then 'A'
when salary>15000 then 'B'
when salary>10000 then 'C'
else 'D'
end
as 工资级别
from employees;	
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
- `unioin`默认去重，如果不去重，可以用 `union all`，查询结果包含重复项

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
INSERT INTO table_name (column1,column2,column3,...)
VALUES (value1,value2,value3,...);

#insert into beauty values(...)	#如果表名后不加字段，则默认为全部字段

#方式二
INSERT INTO table_name
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
UPDATE table_name
SET column1=value1,column2=value2,...
WHERE some_column=some_value;

#2、修改多表的记录
UPDATE table_name1 INNER|LEFT|RIGHT JOIN table_name2 ON 连接条件
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
DELETE FROM table_name
WHERE some_column=some_value;

#2、多表删除

#DELETE table_name1,table_name2 	#表示删除table_name1,table_name2的内容
DELETE table_name1 					#表示删除table_name1的内容
FROM table_name1 INNER|LEFT|RIGHT JOIN table_name2 ON 连接条件
SET column1=value1,column2=value2,...
WHERE some_column=some_value;



#方式二：truncate
truncate table table_name;#删除表里的全部行
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
	CREATE DATABASE  [IF NOT EXISTS][库名];
	```

- 库的修改

	```mysql
	#更改库的字符集
	alter database books character set gbk;
	```

- 库的删除

	```mysql
	DROP DATABASE [IF EXISTS] [库名]
	```

	







```mysql
create database books;
create database if not exists books;#当库存在时，不会报错

drop database if exists books;#当库不存在时，不会报错
```









## 2、表的管理

















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
见删除语句
```



