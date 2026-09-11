# 引入依赖
---
```xml
<!-- Spring Cloud Gateway  -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!-- nacos 服务发现（可选） -->
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!--负载均衡器（可选）-->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```
# 配置
---
在`application.yml`中：
```yaml
spring:  
  cloud:  
    nacos:  
      server-addr: ${hm.nacos.server-addr}:8848  
    gateway:  
      routes:  
        - id: user  # 路由唯一标识
          uri: lb://user-service  # 目标地址，lb:// 代表负载均衡
          predicates:  
            - Path=/user/**  
        - id: item  
          uri: lb://item-service  
          predicates:  
            - Path:/item/**, /search/**
```
