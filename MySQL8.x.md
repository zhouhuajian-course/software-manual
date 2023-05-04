# MySQL 8.x 手册

## 单表索引数量

一般限制最多5个二级表索引

## 进行连接数监控和报警

MySQL 连接非常昂贵，例如可设置达到 1000 报警，到达 2000 就不给再连接。

## 常用SQL

SHOW PROCESSLIST;

SHOW VARIABLES LIKE '%...%';

## 使用存储过程造大量测试数据

stored procedure 存储过程

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
