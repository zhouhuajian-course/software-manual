# CentOS 7.x

## Bash

https://github.com/zhouhuajian-course/linux-shell-script-example

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
