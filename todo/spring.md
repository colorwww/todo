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
 2. 切面(Aspect)：切面规则，（在Spring有@Aspect注解的类才会被进行AOP代理，切面包括切点和通知）
 3. 切点(Point Cut)：需要代理的某一个具体方法
- 切入点表达式常用格式举例如下：
~~~
1.com.wmx.aspect.EmpService.*(..))：表示 com.wmx.aspect.EmpService 类中的任意方法
2.com.wmx.aspect.*.*(..))：表示 com.wmx.aspect 包(不含子包)下任意类中的任意方法
3.com.wmx.aspect..*.*(..))：表示 com.wmx.aspect 包及其子包下任意类中的任意方法
~~~
 4. 通知(Advice)：代理对象执行规则、业务的方法
~~~
有5种增强通知
1. 前置通知@Before
2. 后置通知@After
3. 环绕通知@Around
4. 返回通知@AfterReturning
5. 异常通知@AfterThrowing)
~~~








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
1.@EnableAspectJAutoPropxy会向容器中注册一个AnnotationAwareAspectJAutoProxyCreator的Bean
Definition，
它本身也是一个BeanPostProcessor，它会在registerBeanPostProcessors方法中完成创建
2.在registerBeanPostProcessors中完成creator的创建。
3.执行后置处理器的PostProcessorBeforeInstantiation，
筛选出不需要进行代理的Bean例如AOP中的基础类，提前做好标记(1.Advice,Advisor,Pointcut类型的Bean不需要被代理,2.没有对应的通知需要被应用到这个Bean上的话
//  这个Bean也是不需要被代理的)
4.AOP代理的真正创建时机，postProcessorAfterInstantiation，对需要进行代理的Bean完成AOP代理的创建
（代码中判断如果已经完成代理或者在adviseBeansMap中标记当前bean不需要代理 、或没有对应的通知需要被应用到这个Bean上，则不进行代理，直接返回当前Bean）
~~~

**Spring如何解析出来AOP的通知**
1.获取当前所有的通知 （构建的逻辑就是解析@Aspect注解所标注的类中的方法，通过当前的BeanFactory来获取对应的List<Advisor>）
2.根据前面找出来的Advisor集合进行遍历，然后根据每个Advisor对应的切点来进行匹配，如何合适就返回
![](https://cdn.jsdelivr.net/gh/colorwww/pictures/pictures/1602208704114-1602208704108-%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20201009095808.png)



- Spring AOP 代理是什么？

1. JDK动态代理
2. CGlib动态代理

- 引介(Introduction)是什么？

- 连接点(Joint Point)和切入点(Point Cut)是什么？

- 织入（Weaving）是什么？
### 容器IOC
### Spring生命周期
> 概括：一个Bean的声明周期需要经过3个阶段，实例化，属性注入，初始化，在实例化和初始化前后，又有不同的后置处理器穿插执行，在Bean使用完毕后，又会被销毁

Bean的生命周期只考虑实例化，属性注入，初始化这几部分，首先在Bean生命周期开始之前会调用InstantiationAwareBeanPostProcessor中的postProcessorBeforeInstantiation，执行初始化前的后置处理器，(如果aop中设置了TargetObject的话会在这里返回一个代理Bean)，该处理器执行完返回的Bean不为空的话，会直接返回Bean，结束Bean的生命周期(配置了TargetObject的aop就是这样)，否则就会开始实例化，实例化有4中情形 

一：Spring在5.0版本后加入了一个instanceSupplier属性，是jdk1.8中的lambda表达式，如果配置了该属性，会通过Supplier返回实例化对象，
二：是否配置过工厂方法，此处如果配置了Factory-methods的话只是一个静态工厂，如果有factory-methods的话是一个实例工厂，通过工厂及指定的方法返回实例 
三：是否有指定特定的构造器，有的话使用指定构造器返回实例，
四：走到最后的话就是使用默认构造器来实例化，当然会判断是否有配置过lookup-methods或replace-methods，(这两个方法一般是在原型模式下，用来指定具体的Bean)如果有的话会通过CGLib来返回代理过方法后的对象实例，否则使用默认构造器反射创建实例

实例化之后，属性注入前会合并当前Bean与父级Bean，(合并父级中额外的属性到子Bean中)，还会判断是否要缓存当前的实例，用于解决后面属性注入时循环依赖的问题。
后面会在属性注入的方法内执行postProcessorAfterInstantiation

`属性注入`
属性注入部分要重点关注一个类AbstractAnnotationBeanPostProcessor ,该类继承自InstantiationAwareBeanPostProcessor，重写了处理属性值的方法，在这里它会去BeanMap中获取Bean依赖的属性，如果不存在，会进行该依赖的Bean的生命周期(此处是一个递归)，

`初始化`

初始化部分首先会执行几个实现Aware接口的后置处理器，可以来修改BeanName，classLoader，beanFactory，之后会执行BeanPostProcessor的postProcessorBeforeInitialization，在该部分InitDestoryAnnotationBeanPostProcessor处理器处理了@PostConstruct注解，之后执行初始化方法，InitializingBean#afterPropertiesSet()方法，和init-methods方法
最后执行postProcessorAfterInitialization  至此Bean才可以真正的使用了。

后面再通过preDestory() 或者Disposable.destory()或 destory-methods方法来销毁Bean，至此Bean的生命周期结束


> 1. BeanFactory 和 FactoryBean 的区别
> 2. FactoryBean返回的实例在哪一阶段执行
> 3. 循环依赖是如何解决的
> 4. 讲一讲有哪些后置处理器，分别在什么时候用到，分别起到什么作用
> 5. 在最后初始化的时候，针对AOP，如何判断是用Jdk 还是 CGLib动态代理，讲一下jkd动态代理，CGLib动态代理？
> 6. lambda 表达式很熟悉嘛？来讲讲 Supplier
> 7. 属性注入的不同方法，通过set注入通过构造器注入。。。？
-   这个要分Spring和SpringBoot项目：
1. Spring项目中：首先需要通过

**扫描初始化配置**：BeanFactoryPostProcessor执行，以spring中的为例，在扫描路径下的class后，创建对应的bd，放入到bdmap中，再对map遍历，找到被@Configuration注解的类，进行配置类的生命周期
**实例化**：实现了InstantiationAwareBeanPostProcessor接口的Bean会在这个阶段执行
**属性注入**：注入BeanFactory，BeanNameAware#setName()设置了Bean的名称
**初始化**：实现了BeanPostProcessor接口的bean会在这个阶段执行

	初始化阶段有3个部分会执行，分别是@PostConstract，实现了InitializingBean会执行afterPropertiesSet，init-method
### Spring初始化过程