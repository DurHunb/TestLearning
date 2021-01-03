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

基本结构是由SELECT子句，FROM子句，WHERE子句组成的查询块：

> SELECT 	<字段名表>	FROM 	<表或视图名>	WHERE 	<查询条件>



### 数据操纵语言DML

数据操纵语言DML主要有三种形式：

- 插入：INSERT
- 更新：UPDATE
- 删除：DELETE



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

```
net start MySQL80
net stop MySQL80
```



- 查看服务器版本

```
select version();
```



- 通过Windows自带的客户端登陆/推出MySQL

```
#登录
mysql -h 主机名 -P 端口号 -u 用户名 -p 密码

#退出
exit 或 Ctrl+c
```



- 查看当前所有数据库

```sql
show database;
```



- 打开指定的库

```
use 库名;
```



- 查看当前库的所有的表

```
show tables;
```



- 查看其他库的所有表

```
show tables from 库名;
```



- 创建表

```
create table 表名{
	列名 列类型,
	列名 列类型
}
```



- 查看表结构

```
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

	

## 2、条件查询



```mysql
select [查询列表] from [表名] where [条件]
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



## 3、模糊查询



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





## 4、排序查询



- `asc`——升序，默认为升序
- `desc`——降序

```mysql
select [查询列表] from [表名] where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit
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

















##  5、常见函数



```mysql
# 案例，查询奖金率，如果为NULL，则为0
select ifnull(bonus_rate,0) as 奖金率 from employees;
```



### 单行函数

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









### 分组函数

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
	
	





## 6、分组查询

```mysql
select [查询列表] from [表名] where [条件] 
group by [分组条件]
having [筛选条件]
order by [排序列表] [asc|desc]
limit
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



# 面试题



```mysql
#1、判断以下两种方式执行结果是否一样，并说明原因
select * from employees;
select * from employees where last_name like '%%' and bonus_rate like '%%';

#不一样,如果判断的字段中有NULL值

```

