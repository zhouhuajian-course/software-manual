# MySQL 8.x 手册

## binary logs 数据恢复 全量备份 + 增量备份

假设数据库每天凌晨4点进行一次全量备份

```shell
$ crontab -l
0 4 * * * mysqldump -h127.0.0.1 -uroot -proot --lock-all-tables --flush-logs --routines --databases db1 | gzip > /opt/mysql_db1_$(date +%Y%m%d_%H%M%S).sql.gz
# 模拟凌晨四点已备份，--databases才会生成create database db1;并use db1;的语句，否者不会
$ mysqldump -h127.0.0.1 -uroot -proot --lock-all-tables --flush-logs --routines --databases db1 | gzip > /opt/mysql_db1_$(date +%Y%m%d_%H%M%S).sql.gz
mysqldump: [Warning] Using a password on the command line interface can be insecure.
# 解压查看
$ gzip --decompress mysql_db1_20230531_174438.sql.gz
```

假设进行了一些列的正常操作

```shell
mysql> use db1;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select * from tb1;
+------+------+
| c1   | c2   |
+------+------+
|  100 |  100 |
+------+------+
1 row in set (0.00 sec)

mysql> insert into tb1 values (200, 200);
Query OK, 1 row affected (0.01 sec)

mysql> insert into tb1 values (300, 300);
Query OK, 1 row affected (0.00 sec)
```

假设不小心删掉了 db1 数据库 

```shell
mysql> drop database db1;
Query OK, 1 row affected (0.02 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

恢复数据

1. 先flush logs，让mysql使用新的binlog文件，避免搞混当前有用的binlog文件
2. 进行一次全量恢复；
3. 进行一次drop database前的增量恢复。

```shell
mysql> flush logs;
Query OK, 0 rows affected (0.02 sec)

（增量恢复，使用上一个binlog）

$ mysql -h127.0.0.1 -uroot -proot < mysql_db1_20230531_174438.sql 

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| db1                |
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
+--------------------+
6 rows in set (0.00 sec)

mysql> use db1;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select * from tb1;
+------+------+
| c1   | c2   |
+------+------+
|  100 |  100 |
+------+------+
1 row in set (0.00 sec)

mysql> show binlog events in 'binlog.000017';
+---------------+-----+----------------+-----------+-------------+--------------------------------------+
| Log_name      | Pos | Event_type     | Server_id | End_log_pos | Info                                 |
+---------------+-----+----------------+-----------+-------------+--------------------------------------+
| binlog.000017 |   4 | Format_desc    |         1 |         126 | Server ver: 8.0.33, Binlog ver: 4    |
| binlog.000017 | 126 | Previous_gtids |         1 |         157 |                                      |
| binlog.000017 | 157 | Anonymous_Gtid |         1 |         236 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS' |
| binlog.000017 | 236 | Query          |         1 |         310 | BEGIN                                |
| binlog.000017 | 310 | Table_map      |         1 |         359 | table_id: 184 (db1.tb1)              |
| binlog.000017 | 359 | Write_rows     |         1 |         403 | table_id: 184 flags: STMT_END_F      |
| binlog.000017 | 403 | Xid            |         1 |         434 | COMMIT /* xid=272 */                 |
| binlog.000017 | 434 | Anonymous_Gtid |         1 |         513 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS' |
| binlog.000017 | 513 | Query          |         1 |         587 | BEGIN                                |
| binlog.000017 | 587 | Table_map      |         1 |         636 | table_id: 184 (db1.tb1)              |
| binlog.000017 | 636 | Write_rows     |         1 |         680 | table_id: 184 flags: STMT_END_F      |
| binlog.000017 | 680 | Xid            |         1 |         711 | COMMIT /* xid=273 */                 |
| binlog.000017 | 711 | Anonymous_Gtid |         1 |         788 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS' |
| binlog.000017 | 788 | Query          |         1 |         889 | drop database db1 /* xid=276 */      |
| binlog.000017 | 889 | Rotate         |         1 |         933 | binlog.000018;pos=4                  | (这个事件，表示开始转向下一个binlog文件，具体位置也提供了)
+---------------+-----+----------------+-----------+-------------+--------------------------------------+
15 rows in set (0.00 sec)

$ cd /usr/local/mysql/data
$ mysqlbinlog --start-position=4 --stop-position=788 binlog.000017 | mysql -h127.0.0.1 -uroot -proot

# 数据完全恢复，到删库前
mysql> select * from tb1;
+------+------+
| c1   | c2   |
+------+------+
|  100 |  100 |
|  100 |  100 |
|  200 |  200 |
+------+------+
3 rows in set (0.00 sec)
```

## binary logs 恢复刚创建并刚删除的库

正常操作

```shell
mysql> create database db1;
Query OK, 1 row affected (0.00 sec)

mysql> use db1;
Database changed

mysql> create table tb1 (c1 int, c2 int);
Query OK, 0 rows affected (0.01 sec)

mysql> insert into tb1 values (100, 100);
Query OK, 1 row affected (0.02 sec)

mysql> select * from tb1;
+------+------+
| c1   | c2   |
+------+------+
|  100 |  100 |
+------+------+
1 row in set (0.00 sec)
```

不小心删除 db1

```shell
mysql> drop database db1;
Query OK, 1 row affected (0.01 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

使用binlog恢复前面正常操作出来的数据

```shell
mysql> show master status;
+---------------+----------+--------------+------------------+-------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+---------------+----------+--------------+------------------+-------------------+
| binlog.000013 |     1346 |              |                  |                   |
+---------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

mysql> show binlog events in 'binlog.000013'\G

# 查到创建db1，到删除db1的事件，省略了前面的事件
*************************** 8. row ***************************
   Log_name: binlog.000013
        Pos: 590
 Event_type: Query
  Server_id: 1
End_log_pos: 695
       Info: create database db1 /* xid=10 */
*************************** 9. row ***************************
   Log_name: binlog.000013
        Pos: 695
 Event_type: Anonymous_Gtid
  Server_id: 1
End_log_pos: 772
       Info: SET @@SESSION.GTID_NEXT= 'ANONYMOUS'
*************************** 10. row ***************************
   Log_name: binlog.000013
        Pos: 772
 Event_type: Query
  Server_id: 1
End_log_pos: 891
       Info: use `db1`; create table tb1 (c1 int, c2 int) /* xid=16 */
*************************** 11. row ***************************
   Log_name: binlog.000013
        Pos: 891
 Event_type: Anonymous_Gtid
  Server_id: 1
End_log_pos: 970
       Info: SET @@SESSION.GTID_NEXT= 'ANONYMOUS'
*************************** 12. row ***************************
   Log_name: binlog.000013
        Pos: 970
 Event_type: Query
  Server_id: 1
End_log_pos: 1044
       Info: BEGIN
*************************** 13. row ***************************
   Log_name: binlog.000013
        Pos: 1044
 Event_type: Table_map
  Server_id: 1
End_log_pos: 1093
       Info: table_id: 94 (db1.tb1)
*************************** 14. row ***************************
   Log_name: binlog.000013
        Pos: 1093
 Event_type: Write_rows
  Server_id: 1
End_log_pos: 1137
       Info: table_id: 94 flags: STMT_END_F
*************************** 15. row ***************************
   Log_name: binlog.000013
        Pos: 1137
 Event_type: Xid
  Server_id: 1
End_log_pos: 1168
       Info: COMMIT /* xid=17 */
*************************** 16. row ***************************
   Log_name: binlog.000013
        Pos: 1168
 Event_type: Anonymous_Gtid
  Server_id: 1
End_log_pos: 1245
       Info: SET @@SESSION.GTID_NEXT= 'ANONYMOUS'
*************************** 17. row ***************************
   Log_name: binlog.000013
        Pos: 1245
 Event_type: Query
  Server_id: 1
End_log_pos: 1346
       Info: drop database db1 /* xid=19 */
17 rows in set (0.00 sec)
```

 Pos: 590 -> End_log_pos: 1245
 
```shell
$ cd /usr/local/mysql/data
$ mysqlbinlog --start-position=590 --stop-position=1245 binlog.000013 | mysql -h127.0.0.1 -uroot -proot
mysql: [Warning] Using a password on the command line interface can be insecure.
$ mysql -h127.0.0.1 -p
Enter password: 

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| db1                |
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
+--------------------+
6 rows in set (0.00 sec)

mysql> use db1;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select * from tb1;
+------+------+
| c1   | c2   |
+------+------+
|  100 |  100 |
+------+------+
1 row in set (0.00 sec)
```

## 副本/主从

注：全新两个实例，省略主mysqldump备份，然后从mysql执行

主

```shell
$ mysql --host 127.0.0.1 --port 3307 

mysql> CREATE USER 'repl'@'%' IDENTIFIED WITH mysql_native_password BY 'repl';
# 默认的caching_sha2_password，从连接主时，show replica status，会出现错误
# Message: Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection.
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW MASTER STATUS\G
*************************** 1. row ***************************
             File: binlog.000007
         Position: 690
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)
```

从

```shell
$ mysql -h127.0.0.1 --port=3308

mysql>  CHANGE REPLICATION SOURCE TO 
    ->     SOURCE_HOST='192.168.1.205',
    ->     SOURCE_PORT=3307,
    ->     SOURCE_USER='repl',
    ->     SOURCE_PASSWORD='repl',
    ->     SOURCE_LOG_FILE='binlog.000007',
    ->     SOURCE_LOG_POS=690; \c

mysql> CHANGE REPLICATION SOURCE TO SOURCE_HOST='192.168.1.205', SOURCE_PORT=3307, SOURCE_USER='repl', SOURCE_PASSWORD='repl', SOURCE_LOG_FILE='binlog.000007', SOURCE_LOG_POS=1254;

mysql> START REPLICA;
Query OK, 0 rows affected (0.01 sec)

# 重要看 Replica_IO_Running Replica_SQL_Running 都要为 Yes，
# 否者看这里的错误日志，并修复
mysql> SHOW REPLICA STATUS\G
*************************** 1. row ***************************
             Replica_IO_State: Waiting for source to send event
                  Source_Host: 192.168.1.205
                  Source_User: repl
                  Source_Port: 3307
                Connect_Retry: 60
              Source_Log_File: binlog.000007
          Read_Source_Log_Pos: 1254
               Relay_Log_File: centos-relay-bin.000002
                Relay_Log_Pos: 323
        Relay_Source_Log_File: binlog.000007
           Replica_IO_Running: Yes
          Replica_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Source_Log_Pos: 1254
              Relay_Log_Space: 534
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Source_SSL_Allowed: No
           Source_SSL_CA_File: 
           Source_SSL_CA_Path: 
              Source_SSL_Cert: 
            Source_SSL_Cipher: 
               Source_SSL_Key: 
        Seconds_Behind_Source: 0
Source_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Source_Server_Id: 3307
                  Source_UUID: e5a25096-fec1-11ed-8f94-000c29dc1b1f
             Source_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
    Replica_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Source_Retry_Count: 86400
                  Source_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Source_SSL_Crl: 
           Source_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Source_TLS_Version: 
       Source_public_key_path: 
        Get_Source_public_key: 0
            Network_Namespace: 
1 row in set (0.00 sec)

# 如果要停止
# mysql> STOP REPLICA;
# Query OK, 0 rows affected, 1 warning (0.00 sec)
```

测试

主

```shell
mysql> create database test;
Query OK, 1 row affected (0.00 sec)

mysql> use test;
Database changed
mysql> create table table1 (column1 int, column2 int);
Query OK, 0 rows affected (0.02 sec)

mysql> insert into table1 values (100, 200);
Query OK, 1 row affected (0.03 sec)

mysql> select * from table1;
+---------+---------+
| column1 | column2 |
+---------+---------+
|     100 |     200 |
+---------+---------+
1 row in set (0.00 sec)

mysql> show master status\G
*************************** 1. row ***************************
             File: binlog.000007
         Position: 1932
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)
```

从 

```shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
5 rows in set (0.00 sec)

mysql> use test;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+----------------+
| Tables_in_test |
+----------------+
| table1         |
+----------------+
1 row in set (0.00 sec)

mysql> select * from table1;
+---------+---------+
| column1 | column2 |
+---------+---------+
|     100 |     200 |
+---------+---------+
1 row in set (0.00 sec)

# 留意 Exec_Source_Log_Pos: 1932
mysql> SHOW REPLICA STATUS\G
*************************** 1. row ***************************
             Replica_IO_State: Waiting for source to send event
                  Source_Host: 192.168.1.205
                  Source_User: repl
                  Source_Port: 3307
                Connect_Retry: 60
              Source_Log_File: binlog.000007
          Read_Source_Log_Pos: 1932
               Relay_Log_File: centos-relay-bin.000002
                Relay_Log_Pos: 1001
        Relay_Source_Log_File: binlog.000007
           Replica_IO_Running: Yes
          Replica_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Source_Log_Pos: 1932
              Relay_Log_Space: 1212
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Source_SSL_Allowed: No
           Source_SSL_CA_File: 
           Source_SSL_CA_Path: 
              Source_SSL_Cert: 
            Source_SSL_Cipher: 
               Source_SSL_Key: 
        Seconds_Behind_Source: 0
Source_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Source_Server_Id: 3307
                  Source_UUID: e5a25096-fec1-11ed-8f94-000c29dc1b1f
             Source_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
    Replica_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Source_Retry_Count: 86400
                  Source_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Source_SSL_Crl: 
           Source_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Source_TLS_Version: 
       Source_public_key_path: 
        Get_Source_public_key: 0
            Network_Namespace: 
1 row in set (0.00 sec)

```

## 单机部署多实例

https://dev.mysql.com/doc/refman/8.0/en/mysqld-multi.html

```shell
$ export PATH=$PATH:/usr/local/mysql/bin
$ mysqld_multi --example
$ vim my.cnf
[mysqld_multi]
mysqld     = /usr/local/mysql/bin/mysqld_safe
# mysqladmin位置，用来关闭mysqld
# 调用mysqladmin要用的账号密码
mysqladmin = /usr/local/mysql/bin/mysqladmin
user       = multi_admin
# password   =
pass       = multi_admin

[mysqld3307]
socket     = /tmp/mysql.sock3307
port       = 3307
pid-file   = /usr/local/mysql/multi-mysql/3307_data/3307.pid
datadir    = /usr/local/mysql/multi-mysql/3307_data
user       = mysql
# log-error=/usr/local/mysql/multi-mysql/3307_error.log
server_id  = 3307

[mysqld3308]
socket     = /tmp/mysql.sock3308
port       = 3308
pid-file   = /usr/local/mysql/multi-mysql/3308_data/3308.pid
datadir    = /usr/local/mysql/multi-mysql/3308_data
user       = mysql
# log-error=/usr/local/mysql/multi-mysql/3307_error.log
server_id  = 3308

$ mysqld_multi --defaults-extra-file=my.cnf start # 或指定 3307 
$ mysqld_multi --defaults-extra-file=my.cnf report
Reporting MySQL servers
MySQL server from group: mysqld3307 is running
MySQL server from group: mysqld3308 is running


$ mysql --host 127.0.0.1 --port 3307 -e "show variables like 'server_id';"
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| server_id     | 3307  |
+---------------+-------+
$ mysql --host 127.0.0.1 --port 3307 -e "CREATE USER 'multi_admin'@'localhost' IDENTIFIED BY 'multi_admin';"
$ mysql --host 127.0.0.1 --port 3307 -e "GRANT SHUTDOWN ON *.* TO 'multi_admin'@'localhost';"

$  mysql --host 127.0.0.1 --port 3308 -e "show variables like 'server_id';"
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| server_id     | 3308  |
+---------------+-------+
$ mysql --host 127.0.0.1 --port 3308 -e "CREATE USER 'multi_admin'@'localhost' IDENTIFIED BY 'multi_admin';"
$ mysql --host 127.0.0.1 --port 3308 -e "GRANT SHUTDOWN ON *.* TO 'multi_admin'@'localhost';"

$ mysqld_multi --defaults-extra-file=my.cnf stop
$ mysqld_multi --defaults-extra-file=my.cnf report
Reporting MySQL servers
MySQL server from group: mysqld3307 is not running
MySQL server from group: mysqld3308 is not running
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
