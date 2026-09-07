OpenFeign默认HTTP实现为`HttpURLConnection`，不支持连接池。
# OkHttp
---
## 引入依赖
pom.xml中：
```xml
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-okhttp</artifactId>
</dependency>
```
## 配置
---
application.yml中：
```yaml
feign:
  okhttp: 
    enabled: true
```
