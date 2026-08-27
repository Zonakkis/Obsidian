# 定义
---
>[!note] HDEL
>删除[[哈希 (hash)|哈希]]key中指定的字段。
# 语法
---
```redis
HDEL key field [field ...]
```
# 返回值
---
## 成功
一个整数，表示删除的字段数，其中不包括任何指定了但不存在的字段。
## 失败
### key的值类型不是哈希时
(error) WRONGTYPE Operation against a key holding the wrong kind of value
# 示例
```redis
redis> HSET myhash field1 "foo"
(integer) 1
redis> HDEL myhash field1
(integer) 1
redis> HDEL myhash field2
(integer) 0
```