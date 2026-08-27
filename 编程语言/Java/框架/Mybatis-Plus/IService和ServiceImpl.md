Service接口继承`IService<T>`，Service实现类继承`ServiceImpl<TMapper, T>`，可以获得通用的CRUD方法：
```java
public interface IUserService extends IService<User> {  
}

@Service  
public class UserService extends ServiceImpl<UserMapper, User> implements IUserService {  
  
}
```