# Jenkins 2.x

## 下载安装

下载 war 包

java -jar jenkins.war

需要 java 11 or 17

## 密码

C:\Users\zhouh\.jenkins\secrets\initialAdminPassword

右上角 admin -> 设置 -> 修改密码为 admin

## Github上的shell脚本项目，提交代码后，Jenkins自动构建，将代码部署到多台 Linux 机器

> 如果直接触发式构建，由于Github无法和本地Jenkins通信，所以降级为手动构建，如果需要自动构建，可用Gitlab或Jenkins部署在外网服务器。

> 每个项目都会有自己的工作空间，其实就是项目的工作目录 例如 C:\Users\zhouh\.jenkins\workspace\Test 工作空间也可以自定义为本地代码项目位置

1. 安装插件

Manage Jenkins -> 插件管理 -> Available Plugins 

https://plugins.jenkins.io/publish-over-ssh/

这个插件能够通过ssh发送文件，在远程机器上执行命令， ...

2. 配置多台Linux机器

Dashboard -> Manage Jenkins -> Configure System -> Publish Over SSH

add SSH Servers

192.168.1.205 192.168.1.205 root -> 高级 Use password authentication, or use a different key -> root -> Remote Directory -> /root (基础目录，传输文件的根目录)
Test Configuration -> success

192.168.1.206 192.168.1.206 root -> 高级 Use password authentication, or use a different key -> root -> Remote Directory -> /root (基础目录，传输文件的根目录)
Test Configuration -> success

保存

3. 创建并配置项目

新建Item -> 项目名 Test -> Freestyle Project -> 确定

源码管理 -> Git -> Repository URL -> git@github.com:zhouhuajian-course/software-manual.git -> 指定分支（为空时代表any）-> */main

Build Steps -> Send files or execute commands over SSH 

第一台 192.168.1.205 -> 高级 -> Verbose output in console -> Transfer Set Source files -> test/*.sh -> Remove prefix -> test -> Remote directory -> test (/root/test will be created)   
-> Exec command
```shell
cd /root/test
chmod +x *.sh
```
Add Server
第二台 192.168.1.206 -> 高级 -> Verbose output in console -> Transfer Set Source files -> test/*.sh -> Remove prefix -> test -> Remote directory -> test  
-> Exec command
```shell
cd /root/test
chmod +x *.sh
```
保存

4. 构建项目

Build Now

```
Started by user admin
Running as SYSTEM
Building in workspace C:\Users\zhouh\.jenkins\workspace\Test
The recommended git tool is: NONE
No credentials specified
 > git.exe rev-parse --resolve-git-dir C:\Users\zhouh\.jenkins\workspace\Test\.git # timeout=10
Fetching changes from the remote Git repository
 > git.exe config remote.origin.url git@github.com:zhouhuajian-course/software-manual.git # timeout=10
Fetching upstream changes from git@github.com:zhouhuajian-course/software-manual.git
 > git.exe --version # timeout=10
 > git --version # 'git version 2.36.0.windows.1'
 > git.exe fetch --tags --force --progress -- git@github.com:zhouhuajian-course/software-manual.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git.exe rev-parse "refs/remotes/origin/main^{commit}" # timeout=10
Checking out Revision 122b2a099b7793283a479237be8074d62ccc3118 (refs/remotes/origin/main)
 > git.exe config core.sparsecheckout # timeout=10
 > git.exe checkout -f 122b2a099b7793283a479237be8074d62ccc3118 # timeout=10
Commit message: "Update Jenkins2.x.md"
 > git.exe rev-list --no-walk 122b2a099b7793283a479237be8074d62ccc3118 # timeout=10
SSH: Connecting from host [HP笔记本]
SSH: Connecting with configuration [192.168.1.205] ...
SSH: Creating session: username [root], hostname [192.168.1.205], port [22]
SSH: Connecting session ...
SSH: Connected
SSH: Opening SFTP channel ...
SSH: SFTP channel open
SSH: Connecting SFTP channel ...
SSH: Connected
SSH: cd [/root]
SSH: OK
SSH: cd [/root]
SSH: OK
SSH: cd [test]
SSH: OK
SSH: put [test.sh]
SSH: OK
SSH: Opening exec channel ...
SSH: EXEC: channel open
SSH: EXEC: STDOUT/STDERR from command [cd /root/test
chmod +x *.sh] ...
SSH: EXEC: connected
SSH: EXEC: completed after 202 ms
SSH: Disconnecting configuration [192.168.1.205] ...
SSH: Transferred 1 file(s)
SSH: Connecting from host [HP笔记本]
SSH: Connecting with configuration [192.168.1.206] ...
SSH: Creating session: username [root], hostname [192.168.1.206], port [22]
SSH: Connecting session ...
SSH: Connected
SSH: Opening SFTP channel ...
SSH: SFTP channel open
SSH: Connecting SFTP channel ...
SSH: Connected
SSH: cd [/root]
SSH: OK
SSH: cd [/root]
SSH: OK
SSH: mkdir [test]
SSH: OK
SSH: cd [test]
SSH: OK
SSH: put [test.sh]
SSH: OK
SSH: Opening exec channel ...
SSH: EXEC: channel open
SSH: EXEC: STDOUT/STDERR from command [cd /root/test
chmod +x *.sh] ...
SSH: EXEC: connected
SSH: EXEC: completed after 201 ms
SSH: Disconnecting configuration [192.168.1.206] ...
SSH: Transferred 1 file(s)
Build step 'Send files or execute commands over SSH' changed build result to SUCCESS
Finished: SUCCESS
```

## 源码管理 Subversion

安装插件 Subversion

源码管理 -> Subversion -> Repository URL -> Credentials -> 用户名、密码 

