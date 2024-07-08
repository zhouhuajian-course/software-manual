# RabbitMQ

## Windows 安装 启动

必须先安装 Erlang  
https://www.erlang.org/  
下载安装包，安装  
https://github.com/erlang/otp/releases/download/OTP-27.0/otp_win64_27.0.exe

必须把计算机名改成没中文没空格的名字，否者会有各种 用到计算机名的配置、文件创建

下载 RabbitMQ 安装包 安装  
https://www.rabbitmq.com/  
去掉Service  

D:\RabbitMQ-Server\rabbitmq_server-3.13.4\sbin  
rabbitmq-plugins.bat enable rabbitmq_management  
rabbitmq-server.bat  

http://localhost:15672/ 账号密码 guest guest

## 解决 Management API returned status code 500 

貌似是计算机名有中文的问题  
修改 右键我的电脑 => 属性 => 高级系统设置 => 计算机名 => 修改   
需要重启电脑生效  
卸载rabbitmq  
重装rabbitmq
