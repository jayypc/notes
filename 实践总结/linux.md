# linux

#### netstat

用于显示各种网络相关信息

-a (all)显示所有选项，默认不显示LISTEN相关
-t (tcp)仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化成数字。
-l 仅列出有在 Listen (监听) 的服務状态

#### grep

正则匹配命令

-I(i大写) ：忽略大小写 
-c ：打印匹配的行数 
-l ：从多个文件中查找包含匹配项 
-v ：查找不包含匹配项的行 
-n：打印包含匹配项的行和行标
-A ：显示匹配行的后几行 
-B ：显示匹配行的前几行
-C ：显示匹配行的后前几行


```shell
#显示所有以d开头的文件中包含test的行。
$ grep 'test' d*
#显示在aa，bb，cc文件中匹配test的行。
$ grep 'test' aa bb cc
#显示所有包含每个字符串至少有5个连续小写字符的字符串的行。
$ grep '[a-z]\{5\}' aa

#在event.log中查找包含test内容及其后两行
more event.log|grep 'test' -A 2
```

#### wc

-c 统计字节数。
-l 统计行数。
-m 统计字符数。这个标志不能与 -c 标志一起使用。
-w 统计字数。一个字被定义为由空白、跳格或换行字符分隔的字符串。
-L 打印最长行的长度。

#### 查询某端口监听数量（运用上述三条命令）

```shell
netstat -na|grep 80|wc -l
查询80端口的监听数目
```
#### crontab

-e 编辑crontab服务文件

```
30 2 * * * /DATA1/script/auto.sh
05 22 * * * /usr/sbin/ntpdate 10.142.80.17;

cron文件语法:

分     小时    日       月       星期     命令
0-59   0-23   1-31   1-12     0-6     command     (取值范围,0表示周日一般一行对应一个任务)
记住几个特殊符号的含义:
“*”代表取值范围内的数字,
“/”代表”每”,
“-”代表从某个数字到某个数字,
“,”分开几个离散的数字
```

 -l  列出当前的所有调度任务

 -r  删除所有任务调度工作

查看服务是否已经运行用 ps -ax | grep cron 

#### ntpdate

当Linux服务器的时间不对的时候，可以使用ntpdate工具来校正时间。

```sql
05 22 * * * /usr/sbin/ntpdate 10.142.80.17;
```

#### vi清空

首先，使用gg调整光标到首行，然后使用dG命令即可。

#### df磁盘空间

-a 全部文件系统列表
-h 方便阅读方式显示
-H 等于“-h”，但是计算式，1K=1000，而不是1K=1024
-i 显示inode信息
-k 区块为1024字节
-l 只显示本地文件系统
-m 区块为1048576字节
--no-sync 忽略 sync 命令
-P 输出格式为POSIX
--sync 在取得磁盘信息前，先执行sync命令
-T 文件系统类型

```shell
df -hl 查看本地磁盘剩余空间
df -t ext3 指定磁盘类型
```

#### du目录或文件

-h：以人类可读的方式显示
-a：显示目录占用的磁盘空间大小，还要显示其下目录和文件占用磁盘空间的大小
-s：显示目录占用的磁盘空间大小，不要显示其下子目录和文件占用的磁盘空间大小
-c：显示几个目录或文件占用的磁盘空间大小，还要统计它们的总和
--apparent-size：显示目录或文件自身的大小
-l ：统计硬链接占用磁盘空间的大小
-L：统计符号链接所指向的文件占用的磁盘空间大小

```shell
du -h 文件 
du -ah
du -sh
```

#### more

功能等同cat，只是带有翻页功能，还支持转跳行

按**空白键（space）**就往下一页显示，按 **b** 键就会往回（back）一页显示

```shell
more +3 test.log  #从第三行开始显示日志内容
more -4 test.log  #定义每次翻页4行
```

more, less 都具备查找功能，按/ 然后输入要找的字串，再按 Enter 即可，按 n(next) 会继续找，大写的 N 则是往回(上)找，按 q(quit)或者ZZ离开

#### find命令

```shell
#当前目录查找test文件
find -name test 
#全部目录中查找test文件
find / -name test
```

#### 解压缩

```shell
#zip
zip test.zip testinfo 	#将testinfo压缩成test.zip
unzip test.zip 			#将test.zip解压
#rar linux下不一定能解压rar需要安装有rarlinux
unrar x testl.rar 		#将test.rar解压，x代表解压所有路径
#tar.gz
tar –zvxf test.tar.gz	#将test.tar.gz解压

x : 从 tar 包中把文件提取出来
z : 表示 tar 包是被 gzip 压缩过的，所以解压时需要用 gunzip 解压
v : 显示详细信息
f xxx.tar.gz :  指定被处理的文件是 xxx.tar.gz
```

#### nohup

```shell
nohup your_command > nohup`date +%Y-%m-%d`.out 2>&1 &
```