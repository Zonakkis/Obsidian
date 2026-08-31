# 定义
---
>[!note] HGET
>获取[[哈希 (hash)|哈希]]key中指定的field的值。
# 语法
```redis
HGET key field
```
# 返回值
---
## 成功
一个字符串，表示哈希key中field的值。
## 失败
### key不存在时
(nil)
### key中field不存在时
(nil)

# 示例
```redis
redis> HSET myhash field1 "foo"
(integer) 1
redis> HGET myhash field1
"foo"
redis> HGET myhash field2
(nil)
```