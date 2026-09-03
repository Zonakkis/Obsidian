# 定义
---
>[!note] @Autowired
>@Autowired是Spring提供的用于依赖注入注解，用于标记字段 / 构造方法 / Setter 方法 / 方法参数需要自动注入Bean。

# 三种注入方式
---

|     | 属性注入                   | 构造器注入                     | Setter注入              |
| --- | ---------------------- | ------------------------- | --------------------- |
| 优点  | 代码简洁                   | 依赖不可变(final)、避免NPE、易于单元测试 | 支持可选依赖、支持对象在生命周期中重新配置 |
| 缺点  | 隐藏依赖关系、不能设为不可变(final)、 | 依赖越多构造函数参数越长              |                       |
# 两种注入注解
---

|         | @Autowired           | @Resource                       |
| ------- | -------------------- | ------------------------------- |
| 来源      | Spring框架原生           | Java 标准/JSR-250                 |
| 默认装配规则  | **byType** (先类型，再名称) | **byName** (先名称，再类型)            |
| 匹配属性    | required (默认 true)   | name (指定 beanName), type (指定类型) |
| 支持的注入方式 | 构造器、字段、Setter        | 字段、Setter                       |
| 耦合度     | 与 Spring 强绑定         | 脱离Spring也能兼容                    |

# 注入机制
---
对于@Autowired，Spring查找Bean的机制如下：
- 优先按类型在容器中查找：
	1. 没找到：
		- 默认情况下required = true，抛出 NoSuchBeanDefinitionException。
		- 如果设置了required = false，则跳过注入，字段保持为 null。
	2. 找到一个：直接注入。
	3. 找到多个：逐步执行直到选出Bean。
		1. 检查是否存在`@Primary`标记的Bean，有则优先注入。
		2. 检查是否存在`@Qualifier`标记的Bean，并匹配变量或参数名。
		3. 检查是否有`@Priority`标记。
		4. 按名称查找，按变量或参数的名字在容器内寻找。
		5. 抛出 NoUniqueBeanDefinitionException。
# 注入原理
---
1. **扫描**：容器启动时，扫描classpath，将`@Component`、`@Controller`、`@Service`、`@Repository`和`@Configuration`中`@Bean`等标记的类注册为BeanDefinition。
2. **实例化**：对于非构造注入Bean，调用无参构造函数创建实例；对于构造器注入Bean，创建时就调用对应构造函数进行注入。
3. **填充**：实例化后，`AutowiredAnnotationBeanPostProcessor`会扫描bean的字段和方法，扫描到`@Autowired`就利用反射对字段进行赋值或利用反射调用Setter方法进行赋值。
4. **初始化**：填充完成后，执行@PostConstruct等方法。