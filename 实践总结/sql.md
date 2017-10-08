# sql

#### 常用函数整理

```sql
--字符串处理
substr(a.area,1,2)=b.soapcode --截取前两位
--数字处理
avg(t.fscreen_res_times) --求平均
sum(t.fscreen_res_times) --求和
round(avg(t.fscreen_res_times),2) --保留两位小数
```

#### 判断是否为空

```sql
exch is null 
```
#### case语句加正则匹配所有数字

```sql
case
	when REGEXP_LIKE(netcartrate,'(^[+-]?\d{0,}\.?\d{0,}$)') then
		netcartrate || 'M'
	else
		netcartrate
	end 
		netcartrate

--简单Case函数
CASE sex
WHEN '1' THEN '男'
WHEN '2' THEN '女'
ELSE '其他' END
--Case搜索函数
CASE WHEN sex = '1' THEN '男'
WHEN sex = '2' THEN '女'
ELSE '其他' END
--正则表达式
^ 匹配输入字符串的开始位置
[+-] 匹配指定范围内的任意字符
? 匹配0或1个正好在它之前的那个字符
\d 匹配一个数字字符
{n,} n是一个非负整数。至少匹配前面的n次
$ 匹配输入字符串的结束位置
```
#### update更新语句

```sql
UPDATE temp_userid t
SET t.ctime = (
		SELECT max(b.btime)
		FROM vi_speed_info b
		WHERE b.userid = t.userid
		)
--MAX 函数返回一列中的最大值
```
#### OVER()函数

```sql
UPDATE test_for_search t
SET (
		t.stime
		,t.responserate
		) = (
		SELECT a.stime,a.responserate
		FROM (
			SELECT userid,responserate,stime
			FROM (
				SELECT b.userid
					,b.responserate
					,b.stime
					,row_number() OVER (PARTITION BY b.userid ORDER BY b.stime) AS ind
				FROM v_speed_info_bday b
					,test_for_search t
				WHERE b.userid = t.userid
					AND b.stime BETWEEN to_date('2016-03-10 000000', 'yyyy-mm-dd hh24miss')
						AND to_date('2016-03-10 235959', 'yyyy-mm-dd hh24miss')
				GROUP BY b.userid,b.responserate,b.stime
				)
			WHERE ind = 1
			) a
		WHERE a.userid = t.userid
		)
--分析函数OVER(PARTITION BY)
ROW_NUMBER()返回结果集分区内行的序列号，每个分区的第一行从 1 开始

select decode(grouping(http_url), 1, '综合平均', http_url) http_url,
       avg(t.fscreen_res_times),
       avg(t.homepage_res_times),
       avg(t.homepage_dw_speed),
       avg(t.tcp_delay_times),
       avg(t.http_delay_times),
       avg(t.dns_delay_times),
       avg(t.access_succ_rate),
       avg(t.qoe_value)
  from app_qoe_http_info t
 where stime between to_date('2015-12-03 000000', 'yyyy-mm-dd hh24miss') and
       to_date('2015-12-03 171717', 'yyyy-mm-dd hh24miss')
   and t.band_width in
       (select rate_id from nettype_info where bandrate = '100M')
   and substr(t.citycd, 0, 2) = '08'
 group by rollup(http_url)

--聚合函数
--AVG	返回指定组中的平均值，空值被忽略
select  prd_no,avg(qty) from sales group by prd_no

--COUNT	返回指定组中项目的数量
select  count(prd_no) from sales

--COUNT_BIG  返回指定组中的项目数量，与COUNT函数不同的是COUNT_BIG返回bigint值，而COUNT返回的是int值
select  count_big(prd_no) from sales

--MAX	返回指定数据的最大值
select  prd_no,max(qty) from sales group by prd_no

--MIN  	返回指定数据的最小值
select  prd_no,min(qty) from sales group by prd_no

--SUM  	返回指定数据的和，只能用于数字列，空值被忽略
select  prd_no,sum(qty) from sales group by prd_no

--GROUPING  产生一个附加的列，当用CUBE或ROLLUP运算符添加行时，输出值为1.当所添加的行不是由CUBE或ROLLUP产生时，输出值为0
select  prd_no,sum(qty),grouping(prd_no) from sales group by prd_no with rollup

/*BINARY_CHECKSUM  返回对表中的行或表达式列表计算的二进制校验值，用于检测表中行的更改
CHECKSUM_AGG  返回指定数据的校验值，空值被忽略
CHECKSUM  返回在表的行上或在表达式列表上计算的校验值，用于生成哈希索引
STDEV  返回给定表达式中所有值的统计标准偏差
STDEVP  返回给定表达式中的所有值的填充统计标准偏差
VAR  返回给定表达式中所有值的统计方差
VARP  返回给定表达式中所有值的填充的统计方差*/

--ROLLUP()和CUBE()区别
--ROLLUP()生成简单的 GROUP BY 聚合行以及小计行或超聚合行，还生成一个总计行。
SELECT a, b, c, SUM ( <expression> )
FROM T
GROUP BY ROLLUP (a,b,c)
--会为 (a, b, c)、 (a, b) 和 (a) 值的每个唯一组合生成一个带有小计的行,还将计算一个总计行
--CUBE()生成简单的 GROUP BY 聚合行、ROLLUP 超聚合行和交叉表格行
SELECT a, b, c, SUM (<expression>)
FROM T
GROUP BY CUBE (a,b,c)
--会为 (a, b, c)、 (a, b)、 (a, c)、 (b, c)、 (a)、 (b) 和 (c) 值的每个唯一组合生成一个带有小计的行，还会生成一个总计行
```
#### 增加外键

作用是:最直观的作用是**防止非法数据录入**，就是说录入外键数据时必须在它所属的主表中存在相应数据，如果录入空字符或其他数据会报错。从软件工程的角度看，建立数据库的过程，是从建立物理模型开始的。在物理模型中创建了外键，那么在转化成概念模型甚至于生成sql代码时，就会自动生成相应字段，而不用重复定义。

```sql
create table dept	--公司的部门信息
(deptno number(10) not null,
deptname varchar2(30) not null,
constraint pk_dept primary key(deptno));	--主键

create table emp	--员工的基本信息
( empno number(10) not null,
fname varchar2(20) ,
lname varchar2(20) ,
dept number(10) ,
constraint pk_emp primary key(empno));  --主键

--在员工表增加外键关联到部门表,同时增加级联删除(on delete cascade这句话不加也可以)
alter table emp
add constraint fk_emp_dept foreign key(dept) references dept(deptno) on delete cascade;

delete from dept where deptno = 1;
--删除dept中的一条数据，emp中两条数据也被删除了
```
#### 字符串相加

```sql
cpuusage || '%' cpuusage,
```

#### nvl和nvl2函数

NVL( string1, replace_with) 功能：如果string1为NULL，则NVL函数返回replace_with的值，否则返回string1的值，如果两个参数都为NULL ，则返回NULL。

NVL2(expr1,expr2,expr3) 功能：如果参数表达式expr1值为NULL，则NVL2()函数返回参数表达式expr3的值；如果参数表达式expr1值不为NULL，则NVL2()函数返回参数表达式expr2的值。


```sql
nvl2(cpuusage,cpuusage || '%', 0) cpuusage,
```

#### between

```sql
between to_date('2015-09-23 000000','yyyy-mm-dd hh24miss') 
	and to_date('2015-09-23 175959','yyyy-mm-dd hh24miss')
```

#### decode

将查询结果翻译成其他值

```sql
decode(条件,值1,翻译值1,值2,翻译值2,...值n,翻译值n,缺省值)
```

#### 将表结构导出到word

```sql
SELECT t1.Table_Name AS "表名称",
t3.comments AS "表说明", 
t1.Column_Name AS "字段名称",
t1.Data_Type AS "数据类型",
t1.Data_Length AS "长度",
t1.NullAble AS "是否为空",
t2.Comments AS "字段说明",
t1.Data_Default "默认值"
FROM cols t1 left join user_col_comments t2
on t1.Table_name=t2.Table_name and t1.Column_Name=t2.Column_Name
left join user_tab_comments t3 
on t1.Table_name=t3.Table_name 
WHERE NOT EXISTS ( SELECT t4.Object_Name FROM User_objects t4
WHERE t4.Object_Type='TABLE' 
AND t4.Temporary='Y' 
AND t4.Object_Name=t1.Table_Name )
ORDER BY t1.Table_Name, t1.Column_ID;
```

#### 修改表字段varchar长度

```sql
oracle下这么执行
alter table member modify phone char(15);
sqlserver下这么执行
alter table member alter column phone char(15);
```

#### exists,in用法

> in 是把外表和内表作hash 连接，而exists是对外表作loop循环，每次loop循环再对内表进行查询。
>
> Exists是存在判断，只要有其中一个存在就返回。in是返回所有包含在in中的数据。
>
>  exists 与 in 最大的区别在于 in引导的子句只能返回一个字段
>
> 强调的是是否返回结果集，不要求知道返回什么

#### START WITH CONNECT BY PRIOR子句实现递归查询

> prior 后面跟着的列代表的是本条记录中的列

![Alt description](https://raw.githubusercontent.com/jayypc/notes/master/images/sql1.png)
![Alt description](https://raw.githubusercontent.com/jayypc/notes/master/images/sql2.png)