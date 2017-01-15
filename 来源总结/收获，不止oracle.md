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


