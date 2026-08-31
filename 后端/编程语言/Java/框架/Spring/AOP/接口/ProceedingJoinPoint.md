# 定义
>[!note] `ProceedingJoinPoint`
>[[JoinPoint]]的子接口，能够手动执行被拦截方法。
# 方法
## proceed()
`Object proceed() throws Throwable;`
执行目标方法。
## proceed(Object[] args)
`Object proceed(Object[] args) throws Throwable;`
使用自定义参数执行目标方法。