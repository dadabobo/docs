## 常用Oracle数据库管理

#### 数据库管理
启动停止
```
C:\Windows\syswow64\cmd.exe /k c:\oraclece\app\oracle\product\11.2.0\server\bin\StartDB.bat
C:\Windows\syswow64\cmd.exe /k C:\oraclexe\app\oracle\product\11.2.0\server\bin\StopDB.bat
```
statrtDB.bat
```
@echo off
net start OracleXETNSListener 2>nul
net start OracleServiceXE 2>nul
@oradim -startup -sid XE -starttype inst > nul 2>&1
```
stopDB.bat
```
net stop OracleServiceORCL
```

#### 用户管理

用户管理示例
```sql
--删除
drop user HR_TEST cascade;

--创建
create user HR_TEST identified by hrtest default tablespace TBS_TEST;

--授权
grant connect, resource to HR_TEST; 

--修改密码
alter user HR_TEST identified by hr;

--解锁
alter user HR account unlock; 
```

用户管理
```sql
--查询用户
select * from dba_users where account_status = 'OPEN';

--创建用户
create user username identified by passwd;
create user username identified by passwd default tablespace tablespace_name;

--授权
grant connect, resource to username; 

grant create user,drop user,alter user ,create any view, 
   drop any view,exp_full_database,imp_full_database, 
		 dba,connect,resource,create session  to username;

--删除用户
drop user username cascade;

--说明： 删除了user，只是删除了该user下的schema objects，是不会删除相应的tablespace的。

--修改用户密码
alter user USERNAME identified by PASSWD;

--解锁用户
alter user USERNAME account unlock; 
```

#### 表空间
```sql
--查询用户所用表空间
select username, default_tablespace from dba_users;

--如要找datafile的具体位置
select t1.name, t2.name from v$tablespace t1, v$datafile t2 where t1.ts# = t2.ts#;

--创建表空间 - 示例
create tablespace TBS_TEST datafile 'd:\appdata\oradata\testdata.dbf' size 200m;
create tablespace data_test datafile 'e:\oracle\oradata\test\data_1.dbf' size 2000m;
create tablespace idx_test datafile 'e:\oracle\oradata\test\idx_1.dbf' size 2000m;

--创建表空间
create tablespace TABLESPACE_NAME datafile  'DATAFILE_NAME' size size;

-- 删除表空间 - 示例
drop tablespace TBS_TEST including contents;
drop tablespace TBS_TEST including contents and datafiles;

--删除空的表空间，但是不包含物理文件
drop tablespace tablespace_name;

--删除非空表空间，但是不包含物理文件
drop tablespace tablespace_name including contents;

--删除空表空间，包含物理文件
drop tablespace tablespace_name including datafiles;

--删除非空表空间，包含物理文件
drop tablespace tablespace_name including contents and datafiles;

--如果其他表空间中的表有外键等约束关联到了本表空间中的表的字段，就要加上cascade constraints
drop tablespace tablespace_name including contents and datafiles cascade constraints;

-- 查询表空间使用
-- 使用情况
--查询表空间使用情况
	 SELECT Upper(F.TABLESPACE_NAME)         "表空间名",
       D.TOT_GROOTTE_MB                 "表空间大小(M)",
       D.TOT_GROOTTE_MB - F.TOTAL_BYTES "已使用空间(M)",
       To_char(Round(( D.TOT_GROOTTE_MB - F.TOTAL_BYTES ) / D.TOT_GROOTTE_MB * 100, 2), '990.99')
       || '%'                           "使用比",
       F.TOTAL_BYTES                    "空闲空间(M)",
       F.MAX_BYTES                      "最大块(M)"
	 FROM   (SELECT TABLESPACE_NAME,
               Round(Sum(BYTES) / ( 1024 * 1024 ), 2) TOTAL_BYTES,
               Round(Max(BYTES) / ( 1024 * 1024 ), 2) MAX_BYTES
        FROM   SYS.DBA_FREE_SPACE
        GROUP  BY TABLESPACE_NAME) F,
       (SELECT DD.TABLESPACE_NAME,
               Round(Sum(DD.BYTES) / ( 1024 * 1024 ), 2) TOT_GROOTTE_MB
        FROM   SYS.DBA_DATA_FILES DD
        GROUP  BY DD.TABLESPACE_NAME) D
	 WHERE  D.TABLESPACE_NAME = F.TABLESPACE_NAME
	 ORDER  BY 1;

--查询表空间的free space
select tablespace_name, count(*) AS extends,round(sum(bytes) / 1024 / 1024, 2) AS MB,sum(blocks) AS blocks from dba_free_space group BY tablespace_name;

--查询表空间的总容量
select tablespace_name, sum(bytes) / 1024 / 1024 as MB from dba_data_files group by tablespace_name;

--查询表空间使用率
SELECT total.tablespace_name,
       Round(total.MB, 2)           AS Total_MB,
       Round(total.MB - free.MB, 2) AS Used_MB,
       Round(( 1 - free.MB / total.MB ) * 100, 2)
       || '%'                       AS Used_Pct
FROM   (SELECT tablespace_name,
               Sum(bytes) / 1024 / 1024 AS MB
        FROM   dba_free_space
        GROUP  BY tablespace_name) free,
       (SELECT tablespace_name,
               Sum(bytes) / 1024 / 1024 AS MB
        FROM   dba_data_files
        GROUP  BY tablespace_name) total
WHERE  free.tablespace_name = total.tablespace_name;

--表的实际使用空间
analyze table emp compute statistics; 
select num_rows * avg_row_len from user_tables where table_name = 'EMP';
```


##### 数据字典信息
```sql
--可以查询出所有的用户表
select * from user_tables;

--查询所有表，包括其他用户表
select owner, table_name from all_tables;

--查询出用户所有表的索引
select * from user_indexes;

--查询用户表的索引(非聚集索引):
select * from user_indexes where uniqueness='NONUNIQUE';

--查询用户表的主键(聚集索引):
select * from user_indexes where uniqueness='UNIQUE';

--查询表的索引
select t.*,i.index_type from user_ind_columns t,user_indexes i where t.index_name = i.index_name and
	t.table_name='NODE'

-- 主键与约束
--查询表的主键
select cu.* from user_cons_columns cu, user_constraints au where cu.constraint_name = au.constraint_name and
au.constraint_type = 'P' AND cu.table_name = 'NODE';

--查找表的唯一性约束（包括名称，构成列）：
select column_name from user_cons_columns cu, user_constraints au where cu.constraint_name=au.constraint_name and
cu.table_name='NODE'

--查找表的外键
select * from user_constraints c where c.constraint_type = 'R' and c.table_name='STAFFPOSITION';

--查询外键约束的列名：
select * from user_cons_columns cl where cl.constraint_name = 外键名称;

--查询引用表的键的列名：
select * from user_cons_columns cl where cl.constraint_name = 外键引用表的键名;

--查询表的所有列及其属性
--方法一：
select * from user_tab_columns where table_name=upper('表名');
--方法二：
select cname,coltype,width from col where tname=upper('表名');

--查询一个用户中存在的过程和函数
select object_name,created,status from user_objects 
where lower(object_type) in ('procedure','function');
	
--查询其它角色表的权限
select * from role_tab_privs ;

--更多表结构信息
--执行下列SQL语句，我们将得到更多的关于数据库表结构的信息：
select
 A.column_name 字段名,A.data_type 数据类型,A.data_length 长度,A.data_precision 整数位,
	 A.Data_Scale 小数位,A.nullable 允许空值,A.Data_default 缺省值,B.comments 备注,
	 C.IndexCount 索引次数
from
 user_tab_columns A,
	 user_col_comments B,
	 (select count(*) IndexCount,Column_Name from User_Ind_Columns where Table_Name = ''''TABLE_TEST'''' group by Column_Name) C
where
	 A.Table_Name = B.Table_Name
	 and A.Column_Name = B.Column_Name
	 and A.Column_Name = C.Column_Name(+)
	 and A.Table_Name = ''''TABLE_TEST'''' 
```


#### imp/exp
exp/imp
```
exp usrname/passwd@xe parfile=exp.par
imp usrname/passwd@xe parfile=imp.par
```

exp/imp 示例
```
 exp HR/hr@xe parfile=exp.par
 imp system/system@xe parfile=imp.par

 exp HR_TEST/hr@xe parfile=exp.par
 imp HR_TEST/hr@xe parfile=imp.par
```

`exp.par`文件
```
ROWS=Y
GRANTS=N
DIRECT=N
FEEDBACK=10000
BUFFER=48000000
COMPRESS=N
STATISTICS=NONE  
INDEXES=N
CONSTRAINTS=N
TRIGGERS=N
QUERY="where rownum < 1000"
FILE=expdat.dmp
LOG=exp.log
tables=(
	EMPLOYEES,
	DEPARTMENTS,
	JOBS,
	JOB_HISTORY
)
```
	
`imp.par`文件
```
ROWS=Y
GRANTS=N
COMMIT=N
DATA_ONLY=N
BUFFER=48000000
STATISTICS=NONE  
INDEXES=N
CONSTRAINTS=N
FROMUSER=hr
TOUSER=hr_test
FILE=expdat.dmp
LOG=exp.log 
TABLES=(
	EMPLOYEES,
	DEPARTMENTS
)
```

exp/imp 参数说明
```
参数        说明（默认值）
========    ===============
FULL        -- 整体导出 (N)
COMPRESS    -- 压缩 (Y)
GRANTS      -- 权限 (Y)
ROWS        -- 数据 (Y)
INDEXES     -- 索引 (Y)
CONSTRAINTS -- 约束 (Y)
TRIGGERS    -- 触发器 (Y) 
CONSISTENT  -- 一致性 (N)
DIRECT      -- 直接路径  (N)
BUFFER 		-- 数据缓冲大小
FILE        -- 数据文件
PARFILE     -- 参数文件
LOG         -- 日志文件
OWNER       -- 用户列表
FEEDBACK    -- 每n行显示进度
FILESIZE    -- 每文件最大大小
QUERY       -- 导出查询条件
TABLES      -- 表明列表
STATISTICS  -- 统计信息
```

#### sqlplus
test.sql 示例
```sql
--------------------------------------------
-- test.sql                               --
--------------------------------------------
set define off
spool test.log
	 
set echo off
set feedback off
--set newpage none
set pagesize 5000
set linesize 500
set verify off
set pagesize 0
set term off
--set trims on
set linesize 600
set heading off
set timing off
set verify off
set numwidth 38
				
-- SQL 
select * from user_tables;

-- SQL file
@test2.sql
@@test3.sql

spool off
```

