# oracle

## 基础概念

- 组成:**Oracle数据库**和**数据库实例**

  > oracle数据库: 一系列物理文件的集合（数据文件，控制文件，联机日志，参数文件等）
  >
  > 数据库实例: 一组Oracle后台**进程/线程**以及在服务器分配的共享**内存区**组成
  >
  > 1. 启动Oracle数据库服务器时，实际上是在服务器的内存中创建一个Oracle实例（即在服务器内存中分配共享内存并创建相关的后台内存），然后由这个Oracle数据库实例来访问和控制磁盘中的数据文件。Oracle有一个很大的内存块，称为全局区（SGA）。
  > 2. 安装Oracle后会有默认的实例，即ORCL。一般不创建多个实例，在默认实例下创建表空间和用户等
  > 3. 查询实例名称:`select instance_name from v$instance;`


- 表空间: 用来管理数据存储逻辑概念，表空间只是和数据文件（ORA或者DBF文件）发生关系，数据文件是物理的，**一个表空间可以包含多个数据文件，而一个数据文件只能隶属一个表空间**

- 数据文件(dbf、ora): 数据文件是数据库的物理存储单位。**一旦数据文件被加入到某个表空间后，就不能删除这个文件，如果要删除某个数据文件，只能删除其所属于的表空间才行**

  > 1. 表空间会随机把表数据放到一个或者多个数据文件中
  > 2. 不同用户可以在同一个表空间建立同一个名字的表，这里区分就是用户了！因为表空间只是管理数据存储的一个概念



![oracle1](https://raw.githubusercontent.com/jayypc/notes/master/images/oracle1.jpg)

![oracle2](https://raw.githubusercontent.com/jayypc/notes/master/images/oracle2.jpg)

## 安装

- 启动数据库: 也叫全局数据库，是数据库系统的入口，它会内置一些高级权限的用户如SYS，SYSTEM等。我们用这些高级权限账号登陆就可以在数据库实例中创建表空间，用户，表了。

  ```sql
  查询当前数据库名：select name from v$database;
  ```



# 创建用户

```sql
CREATE USER 用户名称 
PROFILE DEFAULT  --默认deflut 还有 unlimited
IDENTIFIED BY 用户密码 
DEFAULT TABLESPACE 授权访问的表空间名称 
TEMPORARY TABLESPACE TEMP 
ACCOUNT UNLOCK; --创建用户的时候解锁用户，默认该选项 也可以创建一个被锁用户lock
```

- PROFILE: 用来对用户所能使用的数据库资源进行限制的(CPU时间,会话数,登入失败次数等)
- TEMPORARY: 临时表空间主要用途是在数据库进行排序运算[如创建索引、order by及group by、distinct、union/intersect/minus/、sort-merge及join、analyze命令]、管理索引[如创建索引、IMP进行数据导入]、访问视图等操作时提供临时的运算空间，当运算完成之后系统会自动清理。

登陆
sqlplus “/as sysdba”
参数文件位置
show parameter spfile;
控制文件位置
show parameter control;
数据文件位置
select file_name from dba_data_files;
日志文件位置
select group#,member from v$logfile;
归档文件位置
show parameter recovery;
告警日志文件
show parameter dump;

监听的查看、开启、关闭
查看
lsnrctl status 
其中Listener Parameter File和Listener Log File 定位监听文件listener.ora及对应日志

关闭监听lsnrctl stop
开启监听lsnrctl start


