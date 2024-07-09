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

必须把计算机名改成没中文没空格的名字，否者会有各种 用到计算机名的配置、文件创建

例如

```
C:\Users\zhouhuajian\AppData\Roaming\RabbitMQ\db>dir
 驱动器 C 中的卷是 Windows
 卷的序列号是 EE83-03BB

 C:\Users\zhouhuajian\AppData\Roaming\RabbitMQ\db 的目录

Mon  19:29    <DIR>          .
Mon  19:33    <DIR>          ..
Mon  19:20               511 rabbit@HP±Ê¼Ç±¾-feature_flags
Mon  19:29    <DIR>          rabbit@HP±Ê¼Ç±¾-mnesia
Mon  19:29    <DIR>          rabbit@HP±Ê¼Ç±¾-plugins-expand
Mon  19:29                 4 rabbit@HP±Ê¼Ç±¾.pid
               2 个文件            515 字节
               4 个目录 19,116,695,552 可用字节

C:\Users\zhouhuajian\AppData\Roaming\RabbitMQ\db>
```

是计算机名有中文的问题  

1. 修改 右键我的电脑 => 属性 => 高级系统设置 => 计算机名 => 修改    需要重启电脑生效  
2. 卸载rabbitmq  
3. 清空 C:\Users\zhouhuajian\AppData\Roaming\RabbitMQ
4. 重启
5. 重装rabbitmq

问题解决

## 默认数据目录

貌似在 C:\Users\zhouhuajian\AppData\Roaming\RabbitMQ
