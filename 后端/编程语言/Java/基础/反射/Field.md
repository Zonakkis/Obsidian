# 定义
`Field`是类的字段信息。
# 获取Field
## Class.getField()
用于获取公有字段。
方法声明：
`public Field getField(String name)`
例：
```java
public class User {  
    public String name;  
    public User(String name) {  
	    this.name = name;  
	}
}

User user = new User("user");
Class userClass = user.getClass();
Field nameField = userClass.getField("Name");
```
## Class.getDeclaredField()
用于获取公有和私有字段。
方法声明：
`public Field getDeclaredField(String name)`
例：
```java
public class User {  
    public String name;  
    public User(String name) {  
	    this.name = name;  
	}
}

User user = new User("user");
Class userClass = user.getClass();
Field nameField = userClass.getDeclaredField("Name");
```
# 访问私有字段
## Field.setAccessible()
用于绕过 Java 访问控制，允许访问 `private`、`protected` 或 `default`访问级别的字段，但无法访问`final`字段。
方法声明：
`public void setAccessible(boolean flag)`
例：
```java
public class User {  
    public String name;  
    public User(String name) {  
	    this.name = name;  
	}
}

User user = new User("user");
Class userClass = user.getClass();
Field nameField = userClass.getDeclaredField("Name");
nameField.setAccessible(true);
```