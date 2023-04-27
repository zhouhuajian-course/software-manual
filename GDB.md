# GNU gdb 

https://sourceware.org/gdb/

## 相关操作

```
attach $pid

focus            打开 gdb 自带的图形界面 tui

b (break)        打断点
n (next)         运行到下一行
s (step)         单步执行，遇到函数会进入
p (print)        输出
c (continue)     让暂停的程序继续运行

q (quit)         退出
```

## 示例

```shell
[root@centos /root]# ps -ef | grep mysql
mysql     10323   4362  1 15:43 pts/4    00:00:21 mysqld --user=mysql --datadir=/mysql-source-code-analysis/build/data
root      11399  11281  0 16:08 pts/8    00:00:00 grep --color=auto mysql
[root@centos /root]# gdb
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-120.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
(gdb) attach 10323
Attaching to process 10323
Reading symbols from /mysql-source-code-analysis/build/runtime_output_directory/mysqld...done.
Reading symbols from /lib64/libpthread.so.0...(no debugging symbols found)...done.
[New LWP 11076]
[New LWP 10382]
[New LWP 10380]
[New LWP 10379]
[New LWP 10378]
[New LWP 10377]
[New LWP 10376]
[New LWP 10375]
[New LWP 10374]
[New LWP 10373]
[New LWP 10372]
[New LWP 10365]
[New LWP 10364]
[New LWP 10363]
[New LWP 10362]
[New LWP 10361]
[New LWP 10360]
[New LWP 10359]
[New LWP 10354]
[New LWP 10353]
[New LWP 10352]
[New LWP 10342]
[New LWP 10341]
[New LWP 10340]
[New LWP 10339]
[New LWP 10338]
[New LWP 10337]
[New LWP 10335]
[New LWP 10334]
[New LWP 10333]
[New LWP 10332]
[New LWP 10331]
[New LWP 10330]
[New LWP 10329]
[New LWP 10328]
[New LWP 10327]
[New LWP 10326]
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
Loaded symbols for /lib64/libpthread.so.0
Reading symbols from /lib64/librt.so.1...(no debugging symbols found)...done.
Loaded symbols for /lib64/librt.so.1
Reading symbols from /lib64/libssl.so.10...Reading symbols from /lib64/libssl.so.10...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libssl.so.10
Reading symbols from /lib64/libcrypto.so.10...Reading symbols from /lib64/libcrypto.so.10...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libcrypto.so.10
Reading symbols from /mysql-source-code-analysis/build/library_output_directory/libprotobuf-lite.so.3.19.4...done.
Loaded symbols for /mysql-source-code-analysis/build/library_output_directory/libprotobuf-lite.so.3.19.4
Reading symbols from /lib64/libdl.so.2...(no debugging symbols found)...done.
Loaded symbols for /lib64/libdl.so.2
Reading symbols from /lib64/libstdc++.so.6...(no debugging symbols found)...done.
Loaded symbols for /lib64/libstdc++.so.6
Reading symbols from /lib64/libm.so.6...(no debugging symbols found)...done.
Loaded symbols for /lib64/libm.so.6
Reading symbols from /lib64/libgcc_s.so.1...(no debugging symbols found)...done.
Loaded symbols for /lib64/libgcc_s.so.1
Reading symbols from /lib64/libc.so.6...(no debugging symbols found)...done.
Loaded symbols for /lib64/libc.so.6
Reading symbols from /lib64/ld-linux-x86-64.so.2...(no debugging symbols found)...done.
Loaded symbols for /lib64/ld-linux-x86-64.so.2
Reading symbols from /lib64/libgssapi_krb5.so.2...Reading symbols from /lib64/libgssapi_krb5.so.2...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libgssapi_krb5.so.2
Reading symbols from /lib64/libkrb5.so.3...Reading symbols from /lib64/libkrb5.so.3...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libkrb5.so.3
Reading symbols from /lib64/libcom_err.so.2...Reading symbols from /lib64/libcom_err.so.2...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libcom_err.so.2
Reading symbols from /lib64/libk5crypto.so.3...Reading symbols from /lib64/libk5crypto.so.3...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libk5crypto.so.3
Reading symbols from /lib64/libz.so.1...Reading symbols from /lib64/libz.so.1...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libz.so.1
Reading symbols from /lib64/libkrb5support.so.0...Reading symbols from /lib64/libkrb5support.so.0...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libkrb5support.so.0
Reading symbols from /lib64/libkeyutils.so.1...Reading symbols from /lib64/libkeyutils.so.1...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libkeyutils.so.1
Reading symbols from /lib64/libresolv.so.2...(no debugging symbols found)...done.
Loaded symbols for /lib64/libresolv.so.2
Reading symbols from /lib64/libselinux.so.1...Reading symbols from /lib64/libselinux.so.1...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libselinux.so.1
Reading symbols from /lib64/libpcre.so.1...Reading symbols from /lib64/libpcre.so.1...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libpcre.so.1
Reading symbols from /lib64/libnss_files.so.2...(no debugging symbols found)...done.
Loaded symbols for /lib64/libnss_files.so.2
Reading symbols from /lib64/libnss_sss.so.2...Reading symbols from /lib64/libnss_sss.so.2...(no debugging symbols found)...done.
(no debugging symbols found)...done.
Loaded symbols for /lib64/libnss_sss.so.2
Reading symbols from /mysql-source-code-analysis/build/lib/plugin/component_reference_cache.so...done.
Loaded symbols for /mysql-source-code-analysis/build/lib/plugin/component_reference_cache.so
0x00007fdb9ec29ddd in poll () from /lib64/libc.so.6
Missing separate debuginfos, use: debuginfo-install glibc-2.17-326.el7_9.x86_64 keyutils-libs-1.5.8-3.el7.x86_64 krb5-libs-1.15.1-55.el7_9.x86_64 libcom_err-1.42.9-19.el7.x86_64 libgcc-4.8.5-44.el7.x86_64 libselinux-2.5-15.el7.x86_64 libstdc++-4.8.5-44.el7.x86_64 openssl-libs-1.0.2k-26.el7_9.x86_64 pcre-8.32-17.el7.x86_64 sssd-client-1.16.5-10.el7_9.15.x86_64 zlib-1.2.7-21.el7_9.x86_64

(gdb) b	Item_timeval_func::val_int
Breakpoint 1 at	0x3817d48: file	/mysql-source-code-analysis/mysql-8.0.33/sql/item_timefunc.cc, line 1630.

(gdb) focus

   lqq/mysql-source-code-analysis/mysql-8.0.33/sql/item_timefunc.ccqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqk
   x1621          !(ltime.hour || ltime.minute || ltime.second || ltime.second_part))                                                                      x
   x1622        ; /* do nothing */                                                                                                                         x
   x1623      else                                                                                                                                         x
   x1624        *incl_endp = true;                                                                                                                         x
   x1625      return ltime.year;                                                                                                                           x
   x1626    }                                                                                                                                              x
   x1627                                                                                                                                                   x
   x1628    longlong Item_timeval_func::val_int() {                                                                                                        x
   x1629      my_timeval tm;                                                                                                                               x
B+>x1630      return val_timeval(&tm) ? 0 : tm.m_tv_sec;                                                                                                   x
   x1631    }                                                                                                                                              x
   x1632                                                                                                                                                   x
   x1633    my_decimal *Item_timeval_func::val_decimal(my_decimal *decimal_value) {                                                                        x
   x1634      my_timeval tm;                                                                                                                               x
   x1635      if (val_timeval(&tm)) {                                                                                                                      x
   x1636        return error_decimal(decimal_value);                                                                                                       x
   x1637      }                                                                                                                                            x
   x1638      return timeval2my_decimal(&tm, decimal_value);                                                                                               x
   x1639    }                                                                                                                                              x
   x1640                                                                                                                                                   x
   x1641    double Item_timeval_func::val_real() {                                                                                                         x
   mqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqj
multi-thre Thread 0x7f5a6 In: Item_timeval_func::val_int                                                                           Line: 1630 PC: 0x3817d68 

(gdb) n
(gdb) s
(gdb) n
(gdb) n
Item_timeval_func::val_int (this=0x7f59f8181638) at /mysql-source-code-analysis/mysql-8.0.33/sql/item_timefunc.cc:1630
(gdb) p	tm->m_tv_sec
$4 = 1682583169
(gdb) c
(gdb) q
```
