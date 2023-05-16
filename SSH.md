# SSH

## bash 开头的 # $

```
某些SSH客户端
root用户开头#
其他用户开头$
```

## 服务器上 用户/.ssh 文件夹

```
id_rsa -> ssh 私钥
id_rsa.pub -> ssh 公钥
known_hosts -> 记录登录用户的主机公钥，避免中间人攻击。每次登录会校对，如果公钥不同，OpenSSH 会发出警告。第一次登录的时候记录，会提示
authorized_keys -> 记录已授权的密钥。用于免密登录
```

## 免密登录

没有生成私钥公钥的先生成

方式一

```shell

(ssh-copy-id Windows默认没有，git软件有提供，git-bash.exe "D:\git\usr\bin\ssh-copy-id")

$ ssh-copy-id root@192.168.1.206
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/zhouhuajian/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.1.206's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@192.168.1.206'"
and check to make sure that only the key(s) you wanted were added.

$ ssh root@192.168.1.206
Last login: Tue May 16 16:37:52 2023 from 192.168.1.102
[root@centos /root]# 
```

方式二

```shell
将公钥发送到机器，最加到authorized_keys，手动创建的authorized_keys，必须权限设置为600，不然无效
cd /root/.ssh
cat id_rsa.pub >> authorized_keys
chmod 600 authorized_keys
```
