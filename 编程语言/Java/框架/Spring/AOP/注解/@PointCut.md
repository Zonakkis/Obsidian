# 定义
---
>[!note] `@PointCut`
>标识一个方法为[[切入点]]，用方法名命名一个[[切入点]]以便于复用。
# 参数
---
## value
`String value() default "";`
[[切入点表达式]]。
# 示例
---
```java
@Aspect  
@Component
public class LogAspect {
	@PointCut("@annotation(com.example.annotation.Log)")
	public void logAnnotation() { }
}
```