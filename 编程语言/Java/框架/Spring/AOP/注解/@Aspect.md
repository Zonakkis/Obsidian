# 定义
---
>[!note] `@Aspect`
>标识一个类为切面类。

>[!warning] 注意
> 只使用`@Aspect`注解并不会让Spring将这个类自动注册成Bean，因此通常需要与`@Component`一起使用。
# 参数
---

# 示例
---
```java
@Aspect  
@Component
public class LogAspect {
	
}
```
