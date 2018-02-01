## MySQL

#### 启动关闭数据库
```
ps -ef | grep mysqld

## 启动
cd /usr/bin
./mysqld_safe &

## 关闭
cd /usr/bin
./mysqladmin -u root -p shutdown


## 用户设置
root@host# mysql -u root -p

use mysql;

INSERT INTO user (host, user, password, select_priv, insert_priv, update_priv) 
  VALUES ('localhost', 'guest', PASSWORD('guest123'), 'Y', 'Y', 'Y');

FLUSH PRIVILEGES;
```


#### 管理MySQL的命令
* `USE db_name`
  选择要操作的Mysql数据库，使用该命令后所有Mysql命令都只针对该数据库。
* `SHOW DATABASES`
  列出 MySQL 数据库管理系统的数据库列表。
* `SHOW TABLES`
  显示指定数据库的所有表，使用该命令前需要使用 use 命令来选择要操作的数据库。
* `SHOW COLUMNS FROM tbl_name`
  显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。
* `SHOW INDEX FROM tbl_name`
  显示数据表的详细索引信息，包括PRIMARY KEY（主键）。
* `SHOW TABLE STATUS LIKE [FROM db_name] [LIKE 'pattern'] \G`: 
  该命令将输出Mysql数据库管理系统的性能及统计信息。
