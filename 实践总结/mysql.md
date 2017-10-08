# mysql

#### 查询版本和位数

```sql
select version();
show variables like '%version_%'

show databases; -- 查看所有数据库
select database(); --查看当前数据库
```
#### 外键的级联删除

```sql
alter table user_image_results_assoc add constraint FK_Reference_1 foreign key (resultid)
      references user_image_results (resultid) on delete restrict on update restrict;
/*restrict是限制的意思*/
/*CASCADE 是级联的意思*/
所以要将上面的改为cascade
```

#### 删除的三种方法

```sql
/*删除内容和定义，释放空间*/
drop table if exists legal_holiday;
drop table tb;

/*删除内容、释放空间但不删除定义，无法级联删除，受外键影响*/
truncate table tb;

/*删除内容不删除定义，不释放空间，可以级联删除，效率底一行行删除*/
delete from tb
```

#### 导出导入

- 导出整个数据库 

　　mysqldump -u用户名 -p密码  数据库名 > 导出的文件名 
　　C:\Users\jack> mysqldump -uroot -pmysql sva_rec  > e:\sva_rec.sql 

- 导出一个表，包括表结构和数据 

　　mysqldump -u用户名 -p 密码  数据库名 表名> 导出的文件名 
　　C:\Users\jack> mysqldump -uroot -pmysql sva_rec date_rec_drv> e:\date_rec_drv.sql 

- 导出一个数据库结构 

　　C:\Users\jack> mysqldump -uroot -pmysql -d sva_rec > e:\sva_rec.sql 

- 导出一个表，只有表结构 

　　mysqldump -u用户名 -p 密码 -d数据库名  表名> 导出的文件名 
　　C:\Users\jack> mysqldump -uroot -pmysql -d sva_rec date_rec_drv> e:\date_rec_drv.sql 

- 导入数据库 

　　常用source 命令 
　　进入mysql数据库控制台， 
　　如mysql -u root -p 
　　mysql>use 数据库 
　　然后使用source命令，后面参数为脚本文件(如这里用到的.sql) 
　　mysql>source d:wcnc_db.sql