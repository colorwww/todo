### @ControllerAdvice解析

### 事务
### AOP
- 描述一下Spring AOP？

- Spring 的优点？
~~~java
是一个轻量级的开发框架，是很多模块的集合，通过这些模块可以方便我们的开发，
比如以下几个模块 **核心容器**，**数据访问/集成**，**AOP**，**Web**，**消息，测试**...
比如Core Container中的core组件是所有模块的集合，Web是实现了Spring MVC，
Beans和Context是实现IOC和依赖注入的基础，AOP实现切面编程。
~~~

- Spring的AOP理解？
> 首先aop包含以下几个概念
 1. 目标对象 Target Object :需要被代理的对象Target
 2. 切面(Aspect)：切面规则，
 3. 切点(Point Cut)：需要代理的某一个具体方法
 4. 通知(Advice)：代理对象执行规则、业务的方法








- 在Spring AOP中关注点(concern)和横切关注点(cross-cutting concern)有什么不同？

- AOP有哪些可用的实现？

- Spring中有哪些不同的通知类型(advice types)？
1. 前置通知@Before
2. 后置通知@After
3. 环绕通知@Around
4. 返回通知@AfterReturning
5. 异常通知@AfterThrowing


- Spring AOP 代理是什么？

1. JDK动态代理
2. CGlib动态代理

- 引介(Introduction)是什么？

- 连接点(Joint Point)和切入点(Point Cut)是什么？

- 织入（Weaving）是什么？
### 容器IOC
### Spring生命周期
### Spring初始化过程
