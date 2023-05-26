# Redis

## 成员唯一有序的集合 sorted sets

A Redis sorted set is a collection of unique strings (members) ordered by an associated score. When more than one string has the same score, the strings are ordered lexicographically. 

分数一样，按字典排序

```redis
# 往有序集合添加成员
redis> ZADD ranking 100 小明 98 小红 89 小兰 70 小青 90 小黑
(integer) 5
# 正排序 0~2 3个成员
redis> ZRANGE ranking 0 2 
1) "小青"
2) "小兰"
3) "小黑"
# 倒排序 0到2 3个成员
redis> ZRANGE ranking 0 2 REV 
1) "小明"
2) "小红"
3) "小黑"
# 倒排序 0到2 3个成员 带分数
redis> ZRANGE ranking 0 2 REV WITHSCORES
1) "小明"
2) "100"
3) "小红"
4) "98"
5) "小黑"
6) "90"
# 倒排序 获取某个成员的排名 0 第一名 1 第二名 ...
redis> ZREVRANK ranking 小红
(integer) 1
# 获取集合里面的成员数量
redis> ZCARD ranking
(integer) 5
```

## 成员唯一无序的集合 sets

A Redis set is an unordered collection of unique strings (members).

```redis
# 往集合添加成员
redis> SADD teacher:1:students 小明 小红 小黑
(integer) 3
# 查看集合所有成员
redis> SMEMBERS teacher:1:students
1) "小红"
2) "小明"
3) "小黑"
# 集合里面成员唯一，再次添加"小明"，结果为0，没有添加成功，因为已存在
redis> SADD teacher:1:students 小明
(integer) 0
redis> SMEMBERS teacher:1:students
1) "小红"
2) "小明"
3) "小黑"
# 判断某个字符串是否是集合成员
redis> SISMEMBER teacher:1:students 小红
(integer) 1
# 集合中成员总数
redis> SCARD teacher:1:students
(integer) 3
# 删除集合中某个成员
redis> SREM teacher:1:students 小红
(integer) 1
redis> SMEMBERS teacher:1:students
1) "小明"
2) "小黑"
redis> SADD teacher:2:students 小明 小青 小兰
(integer) 3
# 计算多个集合的交集
redis> SINTER teacher:1:students teacher:2:students
1) "小明"
```
