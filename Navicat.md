# Navicat

## 某个数据库下，创建函数，并执行

增加存储过程，并执行

双击数据库 -> 选择函数 -> 右键 -> 新建函数 -> 选择过程 -> (不填参数)点击完成

```sql
CREATE DEFINER = CURRENT_USER PROCEDURE `NewProc`()
BEGIN
	#Routine body goes here...

END;
```

修改过程名，添加具体内容

```sql
CREATE DEFINER=`root`@`%` PROCEDURE `create_student_100w`()
BEGIN
  SET @i = 0;
  WHILE (@i < 1000000) DO
    INSERT INTO student_innodb VALUES (NULL, '小明', '这是个人介绍。', 100.00);
    SET @i  = @i + 1;
  END WHILE;
END
```
Ctrl+S保存 -> 右键函数里面的 create_student_100w -> 执行函数
