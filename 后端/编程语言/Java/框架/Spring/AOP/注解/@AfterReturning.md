# 定义
---
>[!note] `@Before`
>声明一个方法为[[后置返回通知]]。
# 参数
---
## value  
```
String value() default "";
```
可以为[[切入点表达式]]，也可以为使用了[[@PointCut]]被声明为切入点的方法。
## pointcut  
```
String pointcut() default "";
```
同[[@AfterReturning#value|value]]，在两者间选择一个使用。
## returning
```
String returning() default "";
```
[[后置返回通知]]中用于获取返回值的参数名。
# 示例
---
![[后置返回通知#^example]]