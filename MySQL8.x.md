# MySQL 8.x 手册

## 单机部署多实例

```shell
$ export PATH=$PATH:/usr/local/mysql/bin
$ mysqld_multi --example
$ vim my.cnf
[mysqld_multi]
mysqld     = /usr/local/mysql/bin/mysqld_safe
# mysqladmin位置，用来关闭mysqld
mysqladmin = /usr/local/mysql/bin/mysqladmin
# 调用mysqladmin要用的账号密码
user       = root
password   =

[mysqld3307]
socket     = /tmp/mysql.sock3307
port       = 3307
pid-file   = /usr/local/mysql/multi-mysql/3307_data/3307.pid
datadir    = /usr/local/mysql/multi-mysql/3307_data
user       = mysql
# log-error=/usr/local/mysql/multi-mysql/3307_error.log

[mysqld3308]
socket     = /tmp/mysql.sock3308
port       = 3308
pid-file   = /usr/local/mysql/multi-mysql/3308_data/3308.pid
datadir    = /usr/local/mysql/multi-mysql/3308_data
user       = mysql
# log-error=/usr/local/mysql/multi-mysql/3307_error.log

$ mysqld_multi --defaults-extra-file=my.cnf start 3307
$ mysqld_multi --defaults-extra-file=my.cnf start 3308
$ mysqld_multi --defaults-extra-file=my.cnf report
Reporting MySQL servers
MySQL server from group: mysqld3307 is running
MySQL server from group: mysqld3308 is running

$ mysql --host 127.0.0.1 --port 3307 -e "show databases;"
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
$ mysql --host 127.0.0.1 --port 3308 -e "show databases;"
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

## 数据库类型

**金额数据类型**

不能用float double不精确，使用decimal

```sql
create table t1 (c1 float(10, 2), c2 double(10,2), c3 decimal(10,2));
insert into t1 values (1234567.23, 1234567.23, 1234567.23);
select * from t1;
-- 1234567.25, 1234567.23, 1234567.23
```

**BLOB TEXT数据类型**

The four BLOB types are TINYBLOB, BLOB, MEDIUMBLOB, and LONGBLOB.   
These differ only in the maximum length of the values they can hold. 

The four TEXT types are TINYTEXT, TEXT, MEDIUMTEXT, and LONGTEXT.   

**不能有默认值的数据类型**

BLOB, TEXT, GEOMETRY or JSON column 'col1' can't have a default value

## 修改用户

修改密码

```sql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.01 sec)
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
```

修改密码，并指定加密方式

```sql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
Query OK, 0 rows affected (0.01 sec)
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
```

远程登录

```sql
mysql> UPDATE mysql.user SET host='%' WHERE user='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)
```


## MVCC（多版本并发控制）

Multi-Version Concurrency Control

## InnoDB 事务

InnoDB 引擎的所有语句都是在事务中执行的。显式/隐式。 （待继续验证）

InnoDB 四种事务隔离级别

## 锁

为什么要加某个锁？不加可行？自己项目遇到类似问题，能否用上类似加锁？

**InnoDB Locking**

https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#:~:text=InnoDB%20implements%20standard%20row-level%20locking%20where%20there%20are,the%20lock%20to%20update%20or%20delete%20a%20row.

```
Shared and Exclusive Lock(s)

  共享/排它锁

Intention Lock(s)

  意向锁。InnoDB支持多粒度锁(multiple granularity locking)，它允许行级锁与表级锁共存，实际应用中，InnoDB使用的是意向锁。
  意向共享锁(intention shared lock, IS)，它预示着，事务有意向对表中的某些行加共享S锁
  意向排它锁(intention exclusive lock, IX)，它预示着，事务有意向对表中的某些行加排它X锁

Record Lock(s)

  记录锁/行锁。

Gap Lock(s)

 间隙锁。SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE; 会锁住10~20的间隙，如果插入 t.c1=15的记录，会被锁，即使表中没有 t.c1=15的记录。

Next-Key Lock(s)

  临键锁。行锁和间隙锁组合起来就是 Next-Key Lock。

Insert Intention Lock(s)

  插入意向锁。

AUTO-INC Lock(s)
  
  自增锁。
  客户端A 插入，为提交，客户端B 插入 需要等待，避免客户端A再次插入，再select，出现ID跳跃等问题

Predicate Lock(s) for Spatial Indexes


```

```
InnoDB有三种行锁的算法：
1，Record Lock：单个行记录上的锁。
2，Gap Lock：间隙锁，锁定一个范围，但不包括记录本身。GAP锁的目的，是为了防止同一事务的两次当前读，出现幻读的情况。
3，Next-Key Lock：1+2，锁定一个范围，并且锁定记录本身。对于行的查询，都是采用该方法，主要目的是解决幻读的问题。
```

表锁、行锁、间隙锁、页锁  
悲观锁、乐观锁  

## 规范

1. 数据库名、表名、列名、索引名 下划线命名法，不需要加db, tb, idx等前缀
2. 等号两边不加空格

## 索引 有效 失效

模型数空运最快 

补充  
有效 IN < > =  
无效 field1 > field2  

## 设置或修改表存储引擎

```sql
CREATE TABLE ( ... ) ENGINE=InnoDB;
ALTER TABLE student ENGINE=MyISAM;
```

## MySQL 用户名+主机 联合主键

```
PRIMARY KEY (`Host`,`User`)
用户名和主机两个列组成主键，所以可以出现相同用户名，不同主机的用户
```

```sql
mysql> show create table mysql.user\G
*************************** 1. row ***************************
       Table: user
Create Table: CREATE TABLE `user` (
  `Host` char(255) CHARACTER SET ascii COLLATE ascii_general_ci NOT NULL DEFAULT '',
  `User` char(32) COLLATE utf8mb3_bin NOT NULL DEFAULT '',
  `Select_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Insert_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Update_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Delete_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Drop_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Reload_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Shutdown_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Process_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `File_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Grant_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `References_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Index_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Alter_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Show_db_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Super_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_tmp_table_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Lock_tables_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Execute_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Repl_slave_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Repl_client_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_view_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Show_view_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_routine_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Alter_routine_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_user_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Event_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Trigger_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_tablespace_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `ssl_type` enum('','ANY','X509','SPECIFIED') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT '',
  `ssl_cipher` blob NOT NULL,
  `x509_issuer` blob NOT NULL,
  `x509_subject` blob NOT NULL,
  `max_questions` int unsigned NOT NULL DEFAULT '0',
  `max_updates` int unsigned NOT NULL DEFAULT '0',
  `max_connections` int unsigned NOT NULL DEFAULT '0',
  `max_user_connections` int unsigned NOT NULL DEFAULT '0',
  `plugin` char(64) COLLATE utf8mb3_bin NOT NULL DEFAULT 'caching_sha2_password',
  `authentication_string` text COLLATE utf8mb3_bin,
  `password_expired` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `password_last_changed` timestamp NULL DEFAULT NULL,
  `password_lifetime` smallint unsigned DEFAULT NULL,
  `account_locked` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Create_role_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Drop_role_priv` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NOT NULL DEFAULT 'N',
  `Password_reuse_history` smallint unsigned DEFAULT NULL,
  `Password_reuse_time` smallint unsigned DEFAULT NULL,
  `Password_require_current` enum('N','Y') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci DEFAULT NULL,
  `User_attributes` json DEFAULT NULL,
  PRIMARY KEY (`Host`,`User`)
) /*!50100 TABLESPACE `mysql` */ ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8mb3_bin STATS_PERSISTENT=0 ROW_FORMAT=DYNAMIC COMMENT='Users and global privileges'
1 row in set (0.00 sec)
```

## 设置变量 当前会话&全局

SET ...=...;                               -- 当前会话有效
SET GLOBAL innodb_status_output_locks=ON;  -- 所有会话有效

## 常用 SHOW

```sql
SHOW PROCESSLIST;  
SHOW VARIABLES LIKE '%...%';  
SHOW CREATE TABLE student;  
SHOW ENGINES\G                (Engine: InnoDB Support: DEFAULT)  
SHOW ENGINE INNODB STATUS
SHOW TABLE student STATUS
```

## MySQL 命令行客户端

```shell
mysql> help

For information about MySQL products and services, visit:
   http://www.mysql.com/
For developer information, including the MySQL Reference Manual, visit:
   http://dev.mysql.com/
To buy MySQL Enterprise support, training, or other products, visit:
   https://shop.mysql.com/

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.  清理当前输入语句
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.  设置语句定界符
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.  发送命令给服务端 垂直展示结果
exit      (\q) Exit mysql. Same as quit. 退出mysql客户端 跟quit一样
go        (\g) Send command to mysql server. 发送命令给服务端
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql. 退出mysql客户都安
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument. 使用另一个数据
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.
resetconnection(\x) Clean session context.
query_attributes Sets string parameters (name1 value1 name2 value2 ...) for the next query to pick up.
ssl_session_data_print Serializes the current SSL session data to stdout or file

For server side help, type 'help contents'
```

如果输入错误，可以直接加\c回车清理，或\q回车退出

use school 可以不需要加分号，use是一个内置命令

## ; \g \G 区别

语句末尾加上 `;` `\g` `\G` 按回车后，都是执行命令，  
区别 `;` `\g` 水平方向展示结果，`\G` 垂直方向展示结果 

## root 用户远程登录

> 注意：数据库名、表名、字段名区分大小写的情况下，可能要修改 例如user='root'改为User='root'

```sql
mysql> SELECT * FROM mysql.user WHERE user='root'\G
*************************** 1. row ***************************
                    Host: localhost
                    User: root
             Select_priv: Y
             Insert_priv: Y
             Update_priv: Y
             Delete_priv: Y
             Create_priv: Y
               Drop_priv: Y
             Reload_priv: Y
           Shutdown_priv: Y
            Process_priv: Y
               File_priv: Y
              Grant_priv: Y
         References_priv: Y
              Index_priv: Y
              Alter_priv: Y
            Show_db_priv: Y
              Super_priv: Y
   Create_tmp_table_priv: Y
        Lock_tables_priv: Y
            Execute_priv: Y
         Repl_slave_priv: Y
        Repl_client_priv: Y
        Create_view_priv: Y
          Show_view_priv: Y
     Create_routine_priv: Y
      Alter_routine_priv: Y
        Create_user_priv: Y
              Event_priv: Y
            Trigger_priv: Y
  Create_tablespace_priv: Y
                ssl_type: 
              ssl_cipher: 0x
             x509_issuer: 0x
            x509_subject: 0x
           max_questions: 0
             max_updates: 0
         max_connections: 0
    max_user_connections: 0
                  plugin: caching_sha2_password
   authentication_string: 
        password_expired: N
   password_last_changed: 2023-04-25 17:44:26
       password_lifetime: NULL
          account_locked: N
        Create_role_priv: Y
          Drop_role_priv: Y
  Password_reuse_history: NULL
     Password_reuse_time: NULL
Password_require_current: NULL
         User_attributes: NULL
1 row in set (0.00 sec)

mysql> UPDATE mysql.user SET host='%' WHERE user='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)
```

## 单表索引数量

一般限制最多5个二级表索引

## 进行连接数监控和报警

MySQL 连接非常昂贵，例如可设置达到 1000 报警，到达 2000 就不给再连接。

## 使用存储过程造大量测试数据

stored procedure 存储过程

```sql
mysql> CREATE DATABASE school;
mysql> USE school;
mysql> CREATE TABLE student (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, name VARCHAR(20), sex CHAR(1), birth DATE);
mysql> DESCRIBE student;
mysql> -- INSERT INTO student VALUES (NULL, 'Joe', 'm', '1999-03-30');
mysql> -- DROP PROCEDURE student_create;
mysql> delimiter //
mysql> CREATE PROCEDURE student_create (IN amount INT, OUT total INT)
BEGIN
  SET @i = 0;
  WHILE (@i < amount) DO
    INSERT INTO student VALUES (NULL, 'Joe', 'm', '1999-03-30');
    SET @i  = @i + 1;
  END WHILE;
  SELECT COUNT(*) INTO total FROM student;
END//
mysql> delimiter ;
mysql> CALL student_create(3, @total);
mysql> SELECT @total;
mysql> CALL student_create(3, @total);
mysql> SELECT @total;
mysql> SELECT * FROM student;
+----+------+------+------------+
| id | name | sex  | birth      |
+----+------+------+------------+
|  1 | Joe  | m    | 1999-03-30 |
|  2 | Joe  | m    | 1999-03-30 |
|  3 | Joe  | m    | 1999-03-30 |
|  4 | Joe  | m    | 1999-03-30 |
|  5 | Joe  | m    | 1999-03-30 |
|  6 | Joe  | m    | 1999-03-30 |
+----+------+------+------------+
6 rows in set (0.00 sec)
```

## COUNT 性能

https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_count

COUNT() 函数是 聚合函数

```
COUNT(expr) [over_clause]

Returns a count of the number of non-NULL values of expr in the rows retrieved by a SELECT statement. The result is a BIGINT value.

If there are no matching rows, COUNT() returns 0. COUNT(NULL) returns 0.
```

**InnoDB**

> InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. There is no performance difference.

SELECT COUNT(*) FROM table;

性能

COUNT(*) = COUNT(1) > COUNT(主键) > COUNT(非主键)

COUNT(非主键) 需要判断 NULL，需要取具体数据，非NULL才加+  
其他不需要取具体数据，InnoDB主键默认认为不为NULL，  
COUNT(主键) 需要做额外的操作，性能稍低  

**MyISAM**

COUNT(*) 没有WHERE条件时，非常快，

因为MyISAM存储引擎会存储精确的行数统计

> For MyISAM tables, COUNT(*) is optimized to return very quickly if the SELECT retrieves from one table, no other columns are retrieved, and there is no WHERE clause. For example:
> mysql> SELECT COUNT(*) FROM student;
> This optimization only applies to MyISAM tables, because an exact row count is stored for this storage engine and can be accessed very quickly. COUNT(1) is only subject to the same optimization if the first column is defined as NOT NULL.

## mysql 客户端直接执行命令

`mysql -h 127.0.0.1 -e "SELECT VERSION();"` 

-e, --execute=name  Execute command and quit. (Disables --force and history file.)
