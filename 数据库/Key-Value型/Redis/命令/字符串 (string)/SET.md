# 定义
---
>[!note] `SET`
>将key的值设为一个[[数据库/Key-Value型/Redis/数据类型/String|字符串]]。如果key的值已经存在，无论原来是什么类型都将被新的字符串覆盖。
^definition
# 语法
---
```redis
SET key value [NX | XX] [GET] [EX seconds | PX milliseconds |
  EXAT unix-time-seconds | PXAT unix-time-milliseconds | KEEPTTL]
```
# 示例
---
```redis
redis> SET mykey "Hello"
"OK"
redis> GET mykey
"Hello"
redis> SET anotherkey "will expire in a minute" EX 60
"OK"
```