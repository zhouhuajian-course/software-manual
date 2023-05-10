# CGDB

https://cgdb.github.io/  
https://github.com/cgdb  

the curses debugger

## 安装

yum install -y cgdb 或 参考官网源码安装

## 基本用法

```
ESC键进入 cgdb 模式

https://cgdb.github.io/docs/CGDB-Mode.html

+ - 调整代码窗口大小
空格 加断点

i重新回到 gdb 模式

b 函数名 打断点
i b      查看断点列表
s        进入函数
finish   完成，跳出函数
```

```shell
$ /mysql-source-code-analysis/build/bin/mysqld --user=mysql --datadir=/mysql-source-code-analysis/build/data
$ ps -ef | grep mysql
$ cgdb
(gdb) attach 9148

2966│     If there is a CREATE TABLE...START TRANSACTION command which
2967│     is not yet committed or rollbacked, then we should allow only
2968│     BINLOG INSERT, COMMIT or ROLLBACK command.
2969│     TODO: Should we really check name of table when we cable BINLOG INSERT ?
2970│   */
2971│   if (thd->m_transactional_ddl.inited() && lex->sql_command != SQLCOM_COMMIT &&
2972│       lex->sql_command != SQLCOM_ROLLBACK &&
2973│       lex->sql_command != SQLCOM_BINLOG_BASE64_EVENT) {
2974│     my_error(ER_STATEMENT_NOT_ALLOWED_AFTER_START_TRANSACTION, MYF(0));
2975│     binlog_gtid_end_transaction(thd);
2976│     return 1;
2977│   }
2978│
2979├>  thd->work_part_info = nullptr;
2980│
2981│   if (thd->optimizer_switch_flag(OPTIMIZER_SWITCH_SUBQUERY_TO_DERIVED))
2982│     lex->add_statement_options(OPTION_NO_CONST_TABLES);
2983│
2984│   /*
2985│     Each statement or replication event which might produce deadlock
2986│     should handle transaction rollback on its own. So by the start of
2987│     the next statement transaction rollback request should be fulfilled
2988│     already.
2989│   */
2990│   assert(!thd->transaction_rollback_request || thd->in_sub_stmt);
2991│   /*
/mysql-source-code-analysis/mysql-8.0.33/sql/sql_parse.cc                                                                                                   (gdb) n
(gdb) n
(gdb) n
(gdb) p thd->query
$1 = {const LEX_CSTRING &(const THD * const)} 0x318d7c2 <THD::query() const>
(gdb) p thd->query()
$2 = (const LEX_CSTRING &) @0x7f73c4006ed8: {str = 0x7f73c400fd40 "show databases", length = 14}
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000032b2013 in mysql_execute_command(THD*, bool)
                                                   at /mysql-source-code-analysis/mysql-8.0.33/sql/sql_parse.cc:2942
        breakpoint already hit 1 time
(gdb)
```

## 问题

打完断点后，貌似要按c才能继续调试，问题待确认  
F7 F8 那些操作貌似无效，还会导致异常退出，问题待确认

