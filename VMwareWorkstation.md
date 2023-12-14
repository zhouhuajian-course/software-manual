# VMware Workstation

## Ubuntu 桌面版安装

```
1. 下载 Ubuntu 桌面版 
   https://ubuntu.com/
   Download -> Get Ubuntu Desktop
   ubuntu-22.04.3-desktop-amd64.iso
2. 创建新的虚拟机 -> 典型(推荐) -> 稍后安装操作系统  -> 
     选择客户机操作系统 Linux 版本 Ubuntu 64 位
   -> 填写虚拟机名、选择存储位置
     -> 自定义硬件 -> 新CD/DVD -> 使用 ISO 镜像文件
     -> 选择下载的 ubuntu-22.04.3-desktop-amd64.iso
   -> 关闭 -> 完成
3. 开启此虚拟机 -> 中文(简体) -> 安装 Ubuntu -> 键盘布局 Chinese Chinese 继续
    -> 正常安装 （可以去掉 安装 Ubuntu 时下载更新） 继续 
   -> 这台计算机似乎没有安装操作系统 
    -> 清除整个磁盘并安装 Ubuntu -> 现在安装 -> 继续
    -> 您在什么地方 -> 地图点击中国 -> 选 Shanghai - 上海 继续
    -> 创建用户 zhouhuajian zhouhuajain
  -> 现在重启
4. 需要 把 CD/DVD 改为自动检测，然后再启动，不然启动不了

隐藏顶部时钟/隐藏顶部

1. sudo apt update
2. 安装Gnome扩展管理器 sudo apt install gnome-shell-extension-manager 
3. 左下角 应用程序启动器 -> 搜索 Extension 打开
4. 搜索 hidetop
5. 最下面 Hide Clock 安装即可
6. 搜索hidetop，选择按下载
7.  Hide Top Bar 下载即可
备注：搜索功能貌似很不好用，多用不同方式尝试
```

## CentOS 7 扩大硬盘空间

1. Virtual Machine Settings -> Hard Disk -> Expand disk capacity -> 设置为40GB (原来20GB) -> Expand

   Warning：磁盘已成功扩展。您必须从操作系统内部对磁盘重新进行分区和扩展文件系统。

2. 进入 CentOS 操作

```shell
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   40G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part 
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sr0              11:0    1  4.4G  0
$ fdisk /dev/sda
Command (m for help): m
Command (m for help): n
Select (default p): 
Partition number (3,4, default 3): 3
First sector (41943040-83886079, default 41943040): 
Last sector, +sectors or +size{K,M,G} (41943040-83886079, default 83886079): 
Command (m for help): w
WARNING: ...
$ fdisk -l
  Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM
/dev/sda3        41943040    83886079    20971520   83  Linux
$ fdisk /dev/sda
Command (m for help): m
Command (m for help): t
Partition number (1-3, default 3): 3
Hex code (type L to list all codes): L
Hex code (type L to list all codes): 8e
Command (m for help): w
WARNING: ...
```

3. 重启虚拟机 继续操作

```shell
$ ll /dev | grep sda
brw-rw----. 1 root disk      8,   0 Apr 24 19:19 sda
brw-rw----. 1 root disk      8,   1 Apr 24 19:19 sda1
brw-rw----. 1 root disk      8,   2 Apr 24 19:19 sda2
brw-rw----. 1 root disk      8,   3 Apr 24 19:19 sda3
$ mkfs.ext4 /dev/sda3
$ pvcreate /dev/sda3
$ pvdisplay
 --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               centos
  PV Size               <19.00 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              4863
  Free PE               0
  Allocated PE          4863
  PV UUID               K5FgQE-pQ4W-gOdF-ePQa-lU0p-jErl-9kIfig
  "/dev/sda3" is a new physical volume of "20.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sda3
  VG Name               
  PV Size               20.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               CVNesH-amwo-dvqo-Dhzc-wnZz-WuPq-L0g9RW
$ vgextend centos /dev/sda3 (centos 是 /dev/sda2的VG Name，如果是其他，那么填成其他)
$ pvdisplay
$ lvextend -L +15G /dev/mapper/centos-root (/dev/mapper/centos-root 是 df -h 里面的文件系统，+20G不行，可能20G不是完全可用。)
$ lvs
$ xfs_growfs /dev/mapper/centos-root
$ df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G   12M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root   32G  9.8G   23G  31% /
/dev/sda1               1014M  185M  830M  19% /boot
tmpfs                    378M     0  378M   0% /run/user/0
```

## This virtual machine appears to be in use.

If this virtual machine is not in use, press the "Take Ownership" button to obtain ownership of it. Otherwise, press the "Cancel" button to avoid damaging it.

Configuration file: E:\vmware\workstation\CentOS-206\CentOS-206.vmx.

出现这原因是因为，虚拟机非正常关闭导致，错误操作可能会导致虚拟机被删除

正确修复步骤：

1. press the "Cancel button"
2. visit "E:\vmware\workstation\CentOS-206"
3. delete all dirs that end with "lck" which means "lock". eg. CentOS-206.vmdk.lck CentOS-206.vmx.lck
4. start the virtual machine
