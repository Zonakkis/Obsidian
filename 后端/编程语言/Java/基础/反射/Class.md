# 定义
`Class<T>`是Java类或接口的运行时信息。

# 获取Class
## Class.class
例：
```java
Class<String> stringClass = String.class;
```
## instance.getClass()
可以在类的实例上调用。
方法声明：
`public final native Class<?> getClass();`
例：
```java
String str = "Hello, World!";  
Class<? extends String> stringClass = str.getClass();
```
## Class.forName()
方法声明：
`public static Class<?> forName(String className)`
例：
```java
Class<String> stringClass = Class.forName("java.lang.String");
```
