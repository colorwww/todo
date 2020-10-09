### @ControllerAdvice解析

### 事务
### AOP
- 描述一下Spring AOP？
1.面向切面编程，可以将与业务无关的模块，但是不同模块都要用到的业务进行封装，减少代码的重复性，降低代码的耦合性，有利于未来代码的扩展。
2 常见的AOP场景：事务处理，日志记录
3 AOP是基于动态代理的，如果代理对象是实现接口的默认用JDK动态代理，否则用CGlib动态代理



- Spring 的优点？
~~~java
是一个轻量级的开发框架，是很多模块的集合，通过这些模块可以方便我们的开发，
比如以下几个模块 *核心容器*，**数据访问/集成**，**AOP**，**Web**，**消息，测试**...
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
1. Spring-AOP(编程式AOP)
> 实现通知接口并添加到代理工厂中，然后从代理工厂获取到代理，再执行方法。
~~~java
/**
  * 测试前置增强和后置增强
  */
  ProxyFactory proxyFactory = new ProxyFactory();//创建代理工厂
  proxyFactory.setTarget(new UserServiceImpl());//射入目标类对象
  proxyFactory.addAdvice(new UserBeforeAdvice());//添加前置增强MethodBeforeAdvice
  proxyFactory.addAdvice(new UserAfterAdvice());//添加后置增强AfterReturningAdvice  
  UserService userService = (UserService) proxyFactory.getProxy();//从代理工厂获取代理
  userService.queryAll();//调用代理的方法
~~~
2. AspectJ注解实现（动态代理）


- Spring中有哪些不同的通知类型(advice types)？
1. 前置通知@Before
2. 后置通知@After
3. 环绕通知@Around
4. 返回通知@AfterReturning
5. 异常通知@AfterThrowing

**AOP是如何应用到Spring的声明周期中的：**
~~~
1.@EnableAspectJAutoPropxy会向容器中注册一个AnnotationAwareAspectJAutoProxyCreator的BeanDefinition，
它本身也是一个BeanPostProcessor，它会在registerBeanPostProcessors方法中完成创建
2.在registerBeanPostProcessors中完成creator的创建。
3.执行后置处理器的PostProcessorBeforeInstantiation，
筛选出不需要进行代理的Bean例如AOP中的基础类，提前做好标记
4.AOP代理的真正创建时机，postProcessorAfterInstantiation，对需要进行代理的Bean完成AOP代理的创建
~~~
![](https://cdn.jsdelivr.net/gh/colorwww/pictures/pictures/1602208704114-1602208704108-%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20201009095808.png)**



- Spring AOP 代理是什么？

1. JDK动态代理
2. CGlib动态代理

- 引介(Introduction)是什么？

- 连接点(Joint Point)和切入点(Point Cut)是什么？

- 织入（Weaving）是什么？
### 容器IOC
### Spring生命周期
> 一个Bean的声明周期需要经过3个阶段，实例化，属性注入，初始化，在实例化和初始化前后，又有不同的后置处理器穿插执行，在Bean使用完毕后，又会被销毁

**扫描初始化配置**：BeanFactoryPostProcessor执行，以spring中的为例，在扫描路径下的class后，创建对应的bd，放入到bdmap中，再对map遍历，找到被@Configuration注解的类，进行配置类的生命周期
**实例化**：实现了InstantiationAwareBeanPostProcessor接口的Bean会在这个阶段执行
**属性注入**：注入BeanFactory，BeanNameAware#setName()设置了Bean的名称
**初始化**：实现了BeanPostProcessor接口的bean会在这个阶段执行

	初始化阶段有3个部分会执行，分别是@PostConstract，实现了InitializingBean会执行afterPropertiesSet，init-method
### Spring初始化过程