# Elasticsearch 8.x

```
groupadd es
useradd es -g es

不允许root用户启动
vim config/elasticsearch.yml
xpack.security.enabled: false  （需要https，需要账号 密码）
bin/elasticsearch --daemonize

默认不允许root用户启动
vim config/kibana.yml
server.host: "0.0.0.0"
bin/kibana &
```
