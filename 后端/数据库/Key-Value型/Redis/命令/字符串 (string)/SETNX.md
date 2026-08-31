# 定义
---
>[!note] `SETEX`
>如果key不存在，将key的值设为一个[[后端/数据库/Key-Value型/Redis/数据类型/String|字符串]]。
>>从 Redis 版本 2.6.12 开始，此命令被视为已弃用。
>可以使用带有`NX`参数的[[后端/数据库/Key-Value型/Redis/命令/字符串 (string)/SET]]替换。
^definition
# 语法
---
```redis
SETNX key value
```
# 示例
---
```redis
redis> SETNX mykey "Hello"
(integer) 1
redis> SETNX mykey "World"
(integer) 0
redis> GET mykey
"Hello"
```