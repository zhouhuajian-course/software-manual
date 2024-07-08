# RabbitMQ

## Windows 安装 启动

必须先安装 Erlang  
https://www.erlang.org/  
下载安装包，安装  
https://github.com/erlang/otp/releases/download/OTP-27.0/otp_win64_27.0.exe

下载 RabbitMQ 安装包 安装  
https://www.rabbitmq.com/  
去掉Service  

D:\RabbitMQ-Server\rabbitmq_server-3.13.4\sbin  
rabbitmq-plugins.bat enable rabbitmq_management  
rabbitmq-server.bat  

http://localhost:15672/ 账号密码 guest guest
