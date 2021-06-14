### 1、需要启动的服务

oracledbconsoleorcl、oraclemtsrecoveryservice、oracleoradb11g_home1t、oracleserviceorcl需要启动

![1623635972558](E:\Typora笔记\oracle\assets\1623635972558.png)

### 2、基本的一些命令

###### oracle表空间使用率
SELECT a.tablespace_name "表空间名称", 
total / (1024 * 1024) "表空间大小(M)", 
free / (1024 * 1024) "表空间剩余大小(M)", 
(total - free) / (1024 * 1024 ) "表空间使用大小(M)", 
total / (1024 * 1024 * 1024) "表空间大小(G)", 
free / (1024 * 1024 * 1024) "表空间剩余大小(G)", 
(total - free) / (1024 * 1024 * 1024) "表空间使用大小(G)", 
round((total - free) / total, 4) * 100 "使用率 %" 
FROM (SELECT tablespace_name, SUM(bytes) free 
FROM dba_free_space 
GROUP BY tablespace_name) a, 
(SELECT tablespace_name, SUM(bytes) total 
FROM dba_data_files 
GROUP BY tablespace_name) b 
WHERE a.tablespace_name = b.tablespace_name 


###### 扩容表空间
ALTER DATABASE DATAFILE 'G:\oracle\oracle\Oracle10g_win32\database\opt\oracle\oradata\orcl\MALLDAT.DBF' RESIZE 3072M;
