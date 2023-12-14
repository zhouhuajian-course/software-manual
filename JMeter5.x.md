# JMeter 5.x

https://jmeter.apache.org/

## HTTP 开头的配置元件

（模拟浏览器行为）

- HTTP信息头管理器
- HTTP Cookie管理器
- HTTP缓存管理器
- HTTP请求默认值
- HTTP授权管理器

## 使用CSV数据

```
Thread Group -> Add -> Config Element -> CSV Data Set Config

Filename: 绝对路径，建议使用Browse C:/Users/zhouhuajian/Desktop/student.csv
File encoding: 有中文设置下编码
Variable Names (comma-delimited): 变量名 如果CSV第一行没有列名，则需要在这里设置，英文逗号分割
Ignore first line(only used if Variable Names is not empty): 如果设置了变量名，并且CSV第一行有列名，那么可以设置为True，忽略CSV的第一行
Delimiter (use '\t' for tab): 分隔符 默认英文逗号
Allow quoted data?: 运行有引号的数据，如果CSV是有引号的数据，要设置为True，例如"小明",'18'
Recycle on EOF?: 循环CSV数据   这两个配置 True-False False-True
Stop thread on EOF?: CSV数据没了，停止线程？
Sharing mode: CSV数据共享模式，
   假色有两个线程组
   All threads：可以理解为所有线程，共用一份CSV数据
   Current thread group: 可以理解为每个线程组，有一份独立的CSV数据
   Current thread: 可以理解为每个线程，有一份独立的CSV数据
   
name,age
小明,18
小红,20   

HTTP Request -> httpbin.org POST /post
Body Data -> name=${name}&age=${age}

View Results Tree -> Request -> Request Body

   POST http://httpbin.org/post

   POST data:
   name=小明&age=18
   
   [no cookies]
```

## 压测接口

1. 单次发送，查看接口结果是否正确，服务端是否报错
2. 增加接口结果断言，需要跟接口异常进行区分的断言，例如code message data某个字段等，避免出现异常接口，却是绿色成功的标记
3. 压测需要至少添加后面四个监听器 查看结果树 聚合报告 jp@gc - Transactions per Second jp@gc - Connect Times Over Time
4. 如果压测过程中出现奇怪报错，没有明确解决方案时，使用以下步骤解决  
   对于服务端，重启服务端、修改服务端端口、将服务端部署到另一台机器  
   对于客户端，使用不同的电脑压测，确保不是电脑本身问题；
5. 压测时间长，可以让图表看起来更加平稳。

## 压测接口步骤

确保 jpgc - Standard Set 和 5 Additional Graphs 插件已安装

1. 测试计划 -> 右键 -> 线程(用户) -> 线程组 或 jp@gc - Stepping Thread Group (阶梯式加压测试) -> 修改线程数等 (ramp-up梯度上升，100线程 10s，10s内启动所有线程)
2. 线程组 -> 右键 -> 取样器 -> HTTP请求 -> 填写 协议 域名或IP 端口 -> http gnu.org 80
3. 增加断言
4. 线程组 -> 右键 -> 监听器 -> 依次添加 查看结果树 聚合报告 jp@gc - Transactions per Second jp@gc - Connect Times Over Time
5. 运行

## 安装 jpgc - Standard Set 和 5 Additional Graphs 插件

JMeter Plugins at Google Code (JP@GC) is a popular third-party plugins set for JMeter

```
5 Additional Graphs

Response Codes
Bytes Throughput
Connect Times
Latency
Hits/s
```

## 手动安装插件

下载插件 jar 包 -> 放到 lib/ext 目录 -> Restart JMeter

## 安装 DI KafkaMeter 和 Kafka Support 插件

选项 -> Plugins Manager -> Available Plugins -> Kafka -> 选中 DI KafkaMeter 和 Kafka Support -> Apply Changes and Restart JMeter

检查

选项 -> Plugins Manager -> Installed Plugins -> Kafka

## 运行

进入 bin 目录 -> cmd

java -jar ApacheJMeter.jar

## 安装 Plugins Manager 

Plugins Manager 可以使用UI的方式 安装、更新、卸载插件，而不是手动操作

https://jmeter-plugins.org/wiki/PluginsManager/

下载 jmeter-plugins-manager-1.8.jar -> 放到 lib/ext 目录 -> 重启 JMeter

选项 -> Plugins Manager

## 放大字体

选项 -> 放大

## 修改外观为IntelliJ外观

选项 -> 外观 -> Darklaf - IntelliJ
