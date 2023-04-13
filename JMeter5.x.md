# JMeter 5.x

https://jmeter.apache.org/

## 压测接口

1. 单次发送，查看接口结果是否正确，服务端是否报错
2. 增加接口结果断言，需要跟接口异常进行区分的断言，例如code message data某个字段等，避免出现异常接口，却是绿色成功的标记
3. 压测需要至少添加后面四个监听器 查看结果树 聚合报告 jp@gc - Transactions per Second jp@gc - Connect Times Over Time
4. 如果压测过程中出现奇怪报错，没有明确解决方案时，使用以下步骤解决  
   对于服务端，重启服务端、修改服务端端口、将服务端部署到另一台机器  
   对于客户端，使用不同的电脑压测，确保不是电脑本身问题。

## 压测接口步骤

确保 5 Additional Graphs 插件已安装

1. 测试计划 -> 右键 -> 线程(用户) -> 线程组 或 jp@gc - Stepping Thread Group
2. 线程组 -> 右键 -> 取样器 -> HTTP请求 -> 填写 协议 域名或IP 端口 -> https gnu.org 80
3. 增加断言
4. 线程组 -> 右键 -> 监听器 -> 依次添加 查看结果树 聚合报告 jp@gc - Transactions per Second jp@gc - Connect Times Over Time
5. 运行

## 安装 jpgc - Standard Set 和 5 Additional Graphs 插件

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
