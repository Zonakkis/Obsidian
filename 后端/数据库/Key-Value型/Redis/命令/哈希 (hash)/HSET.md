# 定义
---
>[!note] HSET
>将[[哈希 (hash)|哈希]]key中的field的值设置为value。
# 语法
---
```redis
HSET key field value [field value ...]
```
# 返回值
---
## 成功
一个整数，表示添加的字段数。
## 失败
### key的值类型不是哈希时
(error) WRONGTYPE Operation against a key holding the wrong kind of value
# 示例
---
```redis
redis> HSET myhash field1 "Hello"
(integer) 1
redis> HGET myhash field1
"Hello"
redis> HSET myhash field2 "Hi" field3 "World"
(integer) 2
redis> HGET myhash field2
"Hi"
redis> HGET myhash field3
"World"
redis> HGETALL myhash
1) "field1"
2) "Hello"
3) "field2"
4) "Hi"
5) "field3"
6) "World"
```