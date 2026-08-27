MyBatis-Plus 内置了多种 JSON 类型处理器，包括 `AbstractJsonTypeHandler` 及其子类 `Fastjson2TypeHandler`、`FastjsonTypeHandler`、`GsonTypeHandler`、`JacksonTypeHandler` 等。
# 标记字段
---
```java
@Data
@TableName(autoResultMap = true) // 必须开启映射注解@TableName(autoResultMap = true)
public class User {

    @TableField(typeHandler = JacksonTypeHandler.class)
    private UserInfo userInfo;
}

@Data
public class UserInfo {
	private String name;
}
```