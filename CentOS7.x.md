# CentOS 7.x

## Bash

https://github.com/zhouhuajian-course/linux-shell-script-example

## 源码安装 mysql 8.0.33

https://dev.mysql.com/doc/mysql-sourcebuild-excerpt/8.0/en/installing-source-distribution.html

```shell
# Preconfiguration setup
$ groupadd mysql
$ useradd -r -g mysql -s /bin/false mysql
# Beginning of source-build specific instructions
$ tar zxvf mysql-VERSION.tar.gz
$ cd mysql-VERSION
$ mkdir bld
$ cd bld
$ cmake .. (会报cmake版本为2，以及boost找不到错误，改为下方命令)
$   cmake3 -DWITH_BOOST=../boost/boost_1_77_0 ..
$ make
$ make install
# End of source-build specific instructions
# Postinstallation setup
$ cd /usr/local/mysql
$ mkdir mysql-files (主要用于数据导入与导出或叫做备份与还原，MySQL备份会产生文件，xxx.sql或xxx.txt。这些文件默认放在mysql-files。)
$ chown mysql:mysql mysql-files
$ chmod 750 mysql-files
$ bin/mysqld --initialize --user=mysql
$ bin/mysql_ssl_rsa_setup
$ bin/mysqld_safe --user=mysql &
# Next command is optional
$ cp support-files/mysql.server /etc/init.d/mysql.server

修改初始密码 留意bin/mysqld --initialize --user=mysql输出的初始密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.01 sec)
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
```

```shell
$ tar -zxvf mysql-boost-8.0.33.tar.gz
$ yum install -y epel-release
$ yum install -y cmake3
$ yum install -y centos-release-scl
$ yum install -y devtoolset-11-gcc devtoolset-11-gcc-c++ devtoolset-11-binutils
$ yum install -y openssl openssl-devel  
$ yum install -y ncurses-devel  
$ mysqlSourcePath=/mysql/mysql-8.0.33
$ cmake3 -DCMAKE_INSTALL_PREFIX=${mysqlSourcePath} -DDOWNLOAD_BOOST=0 -DWITH_BOOST=${mysqlSourcePath}/boost/boost_1_77_0 ${mysqlSourcePath}
$ make
$ make install
...

## 安装 devtoolset 开发工具集 c/c++

```shell
yum install -y centos-release-scl
yum install -y devtoolset-11-gcc devtoolset-11-gcc-c++ devtoolset-11-binutils
```

## 安装 cmake3

```shell
$ yum install -y epel-release
$ yum install -y cmake3
$ cmake3 --version
cmake3 version 3.17.5
CMake suite maintained and supported by Kitware (kitware.com/cmake).
```

> yum install -y cmake 安装的是 cmake 2，推荐安装 cmake 3

## Installing htop

```shell
yum install -y epel-release 
yum install -y htop
```
