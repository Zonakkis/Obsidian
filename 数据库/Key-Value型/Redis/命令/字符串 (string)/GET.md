# 定义
---
>[!note] `GET`
>获取key的值。当key不存在时，返回nil。当key的值类型不为[[数据库/Key-Value型/Redis/数据类型/String|字符串]]时，返回错误。
^definition
# 语法
---
```redis
GET key
```
# 示例
---
```redis
redis> SET mykey "Hello"
"OK"
redis> GET mykey
"Hello"
redis> GET nonexisting
(nil)
redis> GET notastring
(error) WRONGTYPE Operation against a key holding the wrong kind of value
```