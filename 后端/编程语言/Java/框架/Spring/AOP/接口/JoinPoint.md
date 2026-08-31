# 定义
>[!note] `JoinPoint`
>提供[[连接点]]的静态信息。
# 方法
## getArgs()
`Object[] getArgs();`
返回方法参数。
## getThis()
`Object getThis();`  
返回代理对象。
## getTarget()
`Object getTarget();`
返回目标（原始）对象。
## getSignature()
`Signature getSignature();`
返回连接点类型的[[Signature|签名]]。
>[!note] Spring中的Signature
>Spring只支持拦截方法，因此在Spring中[[Signature]]总是[[MethodSignature]]，可以直接使用强制类型转换。