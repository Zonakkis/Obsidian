# 定义
---
>[!note] `SETEX`
>将key的值设为一个[[数据库/Key-Value型/Redis/数据类型/String|字符串]]。如果key的值已经存在，无论原来是什么类型都将被新的字符串覆盖。同时将key设置为seconds秒后过期。
>>从 Redis 版本 2.6.12 开始，此命令被视为已弃用。
>可以使用带有`EX`参数的[[数据库/Key-Value型/Redis/命令/字符串 (string)/SET]]替换。
^definition
# 语法
---
```redis
SETEX key seconds value
```
# 示例
---
```redis
redis> SETEX mykey 10 "Hello"
"OK"
redis> TTL mykey
(integer) 10
redis> GET mykey
"Hello"
```