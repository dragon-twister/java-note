# Spring

## 特性

- Spring最根本的使命：简化Java开发。Spring采取以下4种关键策略减低复杂性：

  基于POJO的轻量级和最小侵入性编程

  通过依赖注入和面向接口实现松耦合

  基于切面和惯例进行声明式编程

  通过切面和模版减少样板示代码

- AOP

  spring的AOP基于动态代理来实现，它可以用来实现权限、事务、日志、异常上的管理
  - @Pointcut
  - @Before 方法执行前
  - @After 方法返回或异常后调用
  - @Around 封装目标方法，自定义执行顺序

- IOC/DI

  控制反转的意思就是本来属于java程序里构建对象的功能交由容器接管，依赖注入就是当程序要使用某个对象时候，容器会把它注入到程序里。

## bean作用域

- singleton：在Spring IOC容器中仅存在一个Bean实例，Bean以单实例的方式存在。
- prototype：一个bean可以定义多个实例。
- request：每次HTTP请求都会创建一个新的Bean。该作用域仅适用于WebApplicationContext环境。
- session：一个HTTP Session定义一个Bean。该作用域仅适用于WebApplicationContext环境.
- globalSession：同一个全局HTTP Session定义一个Bean。该作用域同样仅适用于WebApplicationContext环境.
- bean默认的scope属性是singleton，多例，是为了防止并发问题。比如单例的成员会被多个线程修改通过两个注解实现@Component@Scope("prototype")，单例，是因为没必要每个请求都新建一个对象，这样子既浪费CPU又浪费内存；@Component默认单例

## bean的生命周期

① 通过构造器创建bean实例

② 为bean的属性设置值和对其他bean的引用

③ 调用bean的初始化方法@PostConstruct

④ bean可以使用了

⑤ bean销毁前调用bean的销毁方法@PreDestory

## Spring的设计模式

    代理模式—在AOP和remoting中被用的比较多。
    单例模式—在spring配置文件中定义的bean默认为单例模式。
    工厂模式—BeanFactory用来创建对象的实例。


##  springboot

**相对于spring的优点**

实现了自动配置，如web.xml，Listener、Filter配置、Servlet、log4j、error、数据库连接、spring事务、视图解析器的配置

**starter原理**

springboot会扫描jar包内metainfo目录下的spring.factories,里面指定了需要自动配置的类

**springboot启动顺序**

1 判断应用类型是servlet、reactive、none
2 扫描spring.factories文件，加载初始化器和监听器
3 准备环境、加载参数
4 创建上下文
5 刷新上下文

**获取配置**

    - @ConfigurationProperties找到该资源的前缀, 通过getter、setter方法注入及获取配置
    - Environment
    - @Value

**提高启动速度**

    - 缩小包扫描的范围（ComponentScan）
    
    - 关闭自动配置，自己选择需要的配置类加载


## 注解

  - @ConfigurationProperties注解的作用是把yml或者properties配置文件转化为bean。

    @EnableConfigurationProperties注解的作用是使@ConfigurationProperties注解生效。

  - @SpringBootApplication

    - @ComponentScan
      - 扫描被@Component标识的类，最终生成ioc容器里的bean
    - @EnableAutoConfiguration
      - 扫描jar包里面META-INF的spring-autoconfigure-metadata.properties下配置的自动配置类，加载进容器。实现自动配置
    - @Configuration/@SpringBootConfiguration
      - 声明当前类是一个配置类．可以通过＠Bean注解生成IOC容器管理的bean

## 拦截器

  - 实现HandleInterceptor接口
    - preHandle
      - Controller方法执行前
    - postHandle
      - Controller方法处理完之后，DispatcherServlet进行视图的渲染之前，也就是说在这个方法中你可以对ModelAndView进行操作
    - afterCompletion
      - DispatcherServlet进行视图的渲染之后

## 三级缓存

  缓存其实就是三个Map

  singletonObjects： 一级缓存，存储单例对象，Bean 已经实例化，初始化完成。 
  
  earlySingletonObjects： 二级缓存，存储 singletonObject，这个 Bean 实例化了，还没有初始化。 
  
  singletonFactories： 三级缓存，存储 singletonFactory

  解决循环依赖步骤：
  
  - A 创建过程中需要 B，于是 A 将自己放到三级缓里面 ，去实例化 B

    B 实例化的时候发现需要 A，于是 B 先查一级缓存，没有，再查二级缓存，还是没有，再查三级缓存，找到了！

    然后把三级缓存里面的这个 A 放到二级缓存里面，并删除三级缓存里面的 A

    B 顺利初始化完毕，将自己放到一级缓存里面（此时B里面的A依然是创建中状态）

    然后回来接着创建 A，此时 B 已经创建结束，直接从一级缓存里面拿到 B ，然后完成创建，并将自己放到一级缓存里面


  ![image](https://github.com/dragon-twister/java-note/assets/27356869/5bea74d7-d5c3-4a4d-b91e-cd846ae69f3c)



## Spring WebFlux

  **what**

  WebFlux 内部使用的是响应式编程（Reactive Programming），以 Reactor 库为基础, 基于异步和事件驱动，可以让我们在不扩充硬件资源的前提下，提升系统的吞吐量和伸缩性。

  **应用场景**

  Spring WebFlux 是一个异步非阻塞式的 Web 框架，所以，它特别适合应用在 IO 密集型的服务中，比如微服务网关这样的应用中。

  PS: IO 密集型包括：磁盘IO密集型, 网络IO密集型，微服务网关就属于网络 IO 密集型，使用异步非阻塞式编程模型，能够显著地提升网关对下游服务转发的吞吐量。

## FAQ

- 循环依赖怎么解决

- 如何在启动时执行代码

  - @PosterConstruct

    - Constructor(构造方法) -> @Autowired(依赖注入) -> @PostConstruct(注释的方法)
    - 不会等Autowire全都加载完

  - 实现InitializingBean类，重写afterPropertiesSet

    - InitializingBean.afterPropertiesSet 解决方案比使用 @PostConstruct 更安全，因为如果我们依赖尚未自动注入的 @Autowired 字段，则 @PostConstruct 方法可能会遇到 NullPointerExceptions

  - ApplicationRunner

    - 可以获取命令行参数解析

  - CommandLineRunner

    - 可以获取输入的命令行参数字符串，但无法解析

  - ApplicationListener<ApplicationReadyEvent>

    - 无法获取命令行参数，但可以获取配置文件参数
