# SFTP

OpenSSH secure file transfer protocol。

sftp is a file transfer program, similar to ftp(1), which performs all operations over an encrypted ssh(1) transport. It may also use many features of ssh, such as public key authentication and compression.

执行的所有操作，都是基于SSH。

## CentOS 7 搭建

https://www.openssh.com/

```shell
# 1. 添加用户组sftp，添加用户用户sftpuser，设置密码为123456
$ groupadd sftp
$ useradd -g sftp -s /sbin/nologin sftpuser
$ echo "123456" | passwd --stdin sftpuser
# 2. 设置用户家目录所有者和权限，貌似必须设置所有者为root，权限不超过755
# 默认 drwx------. 2 sftpuser sftp  62 May 29 16:06 sftpuser
$ cd /home/sftpuser
$ chown root:sftp sftpuser
$ chown 755 sftpuser
# 修改后 drwxr-xr-x. 2 root sftp  62 May 29 16:06 sftpuser
# 3. 修改sshd配置
$ cp /etc/sshsshd_config /etc/sshsshd_config.back
$ vim /etc/ssh/sshd_config
# override default of no subsystems
# 注释Subsystem sftp    /usr/libexec/openssh/sftp-server
# 不用sftp-server，貌似是为了不新启动进程来作为sftp服务器，直接内置在sshd
# 添加下面5行
Subsystem sftp internal-sftp
Match User sftpuser
        ChrootDirectory /home/sftpuser
        AllowTcpForwarding no
        ForceCommand internal-sftp
# Example of overriding settings on a per-user basis
#Match User anoncvs
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server
# 4. 重启sshd
$ systemctl restart sshd
# 5. 添加项目要使用的文件夹（有权限操作的就是这个目录）
$ mkdir /home/sftpuser/project1 (project1修改为具体项目名)
$ chown sftpuser:sftp /home/sftpuser/project1
```

测试 (最好用另一台机器)

```shell
$ sftp sftpuser@192.168.1.205
sftpuser@192.168.1.205's password: 
Connected to 192.168.1.205.
sftp> ls
project1  
sftp> cd project1/
sftp> mkdir 123
sftp> mkdir 456
sftp> ls
123  456  
sftp> quit
```
