# oracle

#### 登陆

- `sqlplus` 后输入账号密码

- 普通登陆: `sqlplus scott/123456`  或  `SQL>connect scott/123456`

- os身份登陆:`sqlplus / as sysdba `  或  `SQL>connect / as sysdba`

- 管理员登陆:`sqlplus sys/123456 as sysdba` 或 `SQL>connect sys/123456 as sysdba`

  > 普通的OS验证策略两个特点：一个是服务器本地生效，客户端匿名登录无效。另一个只能登录为管理员sys，不能其他用户。

#### 切换用户

- `connect hr/123456`

#### 创建用户,授权

- `create user 用户名 identified by 密码 default tablespace 表空间; ` 

- ```sql
  grant connect,resource to demo;  
  grant create any sequence to demo;  
  grant create any table to demo;  
  grant delete any table to demo;  
  grant insert any table to demo;  
  grant select any table to demo;  
  grant unlimited tablespace to demo;  
  grant execute any procedure to demo;  
  grant update any table to demo;  
  grant create any view to demo; 

  全局：
  grant unlimited tablespace to abc;
  针对某个表空间：
  alter user abc quota unlimited on test;
  回收：
  revoke unlimited tablespace from abc;
  alter user abc quota 0 on test;


  grant create session to 用户名;  	--授予用户创建session的权限，即登陆权限
  grant create table to 用户名;   	--授予创建表的权限
  grant insert table to 用户名;    	--插入表的权限
  grant update table to 用户名;    	--修改表的权限
  grant create tablespace to 用户名; --授予用户创建表空间的权限
  grant unlimited tablespace to 用户名;--授予用户使用表空间的权限
  grant create view to 用户名;  		--授予用户创建视图的权限
  grant DBA to 用户名;	   --授予DBA

  grant all privileges to public或用户名;	   --授予public(所有用户)或某用户所有权限
  ```

####  创建用户表空间

```sql
create tablespace 表空间名  
datafile ' 路径(要先建好路径)\***.dbf' size *M  
autoextend on  --自动增长  
--还有一些定义大小的命令，看需要 
 default storage(  
 initial 100K,  
 next 100k,  
);  

--例子
create tablespace DEMOSPACE   
datafile 'E:/oracle_tablespaces/DEMOSPACE_TBSPACE.dbf'   
size 1500M   
autoextend on next 5M maxsize 3000M;  

--一般建N个存数据的用户表空间和一个索引表空间
这个索引表空间和用户表空间在物理上没有区别,只是功能划分上的使用,如果是两个硬盘以上,将两个表空间建立在不同硬盘,提高IO效率.
对于一个频繁访问的表,其索引也是频繁访问的,其表和索引同时访问概率较大,为避免wr数据与wr索引之间的竞争可能,把不同的表数据和索引放在不同的表空间中,避免IO竞争.
```

#### 删除表空间

`drop tablespace tablespace_name including contents and datafiles`

#### 删除用户

`drop user user_name cascade`

#### 查询基础信息

​	**查询实例:** `show parameter instance`

​	**查询表空间:** ` select tablespace_name from dba_tablespaces;`

​	**查询当前用户被授予角色:** `select * from user_role_privs` 

​	**查询某用户下所有表及其占用空间:**

```sql
select SEGMENT_NAME, TABLESPACE_NAME, sum(BYTES / 1024 / 1024) || 'M'
  from USER_extents
 where SEGMENT_TYPE = 'TABLE'
 group by SEGMENT_NAME, TABLESPACE_NAME
```

​	**查询所有表及其占用空间:**

```sql
select SEGMENT_NAME, TABLESPACE_NAME, sum(BYTES / 1024 / 1024) || 'M'
  from dba_extents
 where SEGMENT_TYPE = 'TABLE'
 group by SEGMENT_NAME, TABLESPACE_NAME
```

​	**查询表名及其行数(拥有dba权限)**

```sql
select table_name, num_rows, OWNER
  from dba_tables t
 where t.OWNER = 'BJSOC'
   and num_rows != 0
 order by table_name
```

​	**查询表的一条记录最大会占用多少空间**

```sql
select t2.table_name ,  
        (sum(nvl(t2.data_size , 0 )))||' (B)' as  record_size_of_table1  ,   
         round((sum(nvl(t2.data_size , 0 )))/1024 , 4)||' (K)' as  record_size_of_table2
  from 
   (select 
    t1.TABLE_NAME ,  t1.COLUMN_NAME , t1.DATA_TYPE ,
    case 
      when   t1.DATA_TYPE = 'NUMBER' then    floor((nvl(t1.DATA_PRECISION , 0 )+1)/2)+2
      when   t1.DATA_TYPE = 'DATE'   then  7 
      else   t1.DATA_LENGTH  
    end  as  data_size 
   from user_tab_columns  t1 ) t2  
 group by t2.table_name  order by t2.table_name 
```
**11g空表查询SEGMENT**
```
select 'alter table '||t.TABLE_NAME||' allocate extent;' from user_tables t where t.SEGMENT_CREATED = 'NO'
```
**查询用户对应默认表空间**
```
select username ,default_tablespace from dba_users;
```
**重建序列**
```
SELECT *
  FROM (SELECT ' CREATE SEQUENCE ' || SEQUENCE_NAME || ' MINVALUE ' ||
               MIN_VALUE || ' MAXVALUE ' || MAX_VALUE || ' START WITH ' ||
               LAST_NUMBER || ' INCREMENT BY ' || INCREMENT_BY || ' CACHE ' ||
               CACHE_SIZE || ' ;' AS SQL
          FROM USER_SEQUENCES
        UNION
        SELECT ' DROP SEQUENCE ' || SEQUENCE_NAME || ';' AS SQL
          FROM USER_SEQUENCES)
 ORDER BY SQL DESC
```
**修改主键**
```
delete from LOG_USER_SEND;
alter table LOG_USER_SEND drop constraint PK_LOG_USER_SEND;
drop index PK_LOG_USER_SEND;
alter table LOG_USER_SEND modify SEND_ID null;
alter table LOG_USER_SEND modify LOG_ID not null;
alter table LOG_USER_SEND add constraint PK_LOG_USER_SEND primary key (LOG_ID);
commit;
```

#### 导入和导出

- exp导出数据(dmp格式)

```shell
exp scott/tiger@orcl file=D:\database.dmp;
```

- imp导入数据

```shell
imp scott/tiger@orcl file=D:\database.dmp;
```

> 会出现的问题:
>
> 1.导入的用户权限不够,导出用户有DBA权限,解决方案:增加权限grant dba to 用户名;
>
> 2.必须指出需要导入多少数据
>
> imp scott/tiger@orcl full=y file=D:\database.dmp; --全部数据
>
> imp scott/tiger@orcl file=D:\database.dmp tables=(tb1,tb2);  --某些表内容
>
> 3.导入数据已存在
>
> imp scott/tiger@orcl  ignore=y  file=D:\database.dmp;  --忽略存在内容



#### system密码重置(对oracle服务器有管理员权限)

- `sqlplus /nolog` 进入sql命令行
- `conn / as sysdba` 以sysdba登陆
- `alter user system identified by password;` 重置密码为password

#### wm_concat函数

可以把列值以','号分隔起来并显示成一行

```sql
select wm_concat(name) from test;
select replace(wm_concat(name),',','|') from test
```