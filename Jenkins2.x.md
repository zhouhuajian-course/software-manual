# Jenkins 2.x

## 下载安装

下载 war 包

java -jar jenkins.war

需要 java 11 or 17

## 密码

C:\Users\zhouh\.jenkins\secrets\initialAdminPassword

右上角 admin -> 设置 -> 修改密码为 admin

## Github上的shell脚本项目，提交代码后，Jenkins自动构建，将代码部署到多台 Linux 机器

1. 安装插件

Manage Jenkins -> 插件管理 -> Available Plugins 

https://plugins.jenkins.io/publish-over-ssh/

这个插件能够通过ssh发送文件，在远程机器上执行命令， ...

2. 配置多台Linux机器

Dashboard -> Manage Jenkins -> Configure System -> Publish Over SSH

add SSH Servers

192.168.1.205 192.168.1.205 root -> 高级 Use password authentication, or use a different key -> root  
Test Configuration -> success

192.168.1.206 192.168.1.206 root -> 高级 Use password authentication, or use a different key -> root  
Test Configuration -> success

保存

3. 

