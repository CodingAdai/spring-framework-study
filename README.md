# Spring-Framework&Spring-Boot&Spring-Cloud



## Spring Framework

org.springframework.beans和org.springframework.context 包 是 IoC 容器的 基础 。



BeanDefinition
在容器内，Bean定义表示为BeanDefinition对象，含有一些元数据metadata。


BeanFactory
ApplicationContext




> Configuration Metadata（This configuration metadata represents how you, as an application developer, tell the Spring container to instantiate, configure, and assemble the objects in your application.）

* XML-based configuration
* annotation-based configuration
* Java-based configuration




扩展点：
* BeanPostProcessor
* BeanFactoryPostProcessor
* FactoryBean



Spring Framework  5.0以上提供以下几个注解，用于 Null-safety，配合Idea等IDE使用，很不错。

NonNull
NonNullApi
NonNullFields
Nullable


SingletonBeanRegistry


## Spring Boot
特性

* Create stand-alone Spring applications
* Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
* Provide opinionated 'starter' dependencies to simplify your build configuration
* Automatically configure Spring and 3rd party libraries whenever possible
* Provide production-ready features such as metrics, health checks and externalized configuration
* Absolutely no code generation and no requirement for XML configuration


Spring-Boot based Application的启动过程

在Java启动应用时，会根据MANIFEST.MF执行Main-Class配置指定的main类（org.springframework.boot.loader.JarLauncher），由JarLauncher启动Start-Class配置中指定的class。

（依赖库如何加载进JVM：通过替换内建sun.net.protocol.jar.Handler为org.springframework.boot.loader.jar.handler实现内嵌jar加载。）

1、应用进程属于JarLauncher的子进程还是同一进程？





固化的Maven依赖

1、spring-boot-starter-parent （开发者的项目  使用 它作为 parent）

2、spring-boot-dependencies （本身就是一个pom文件）

http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html

传递依赖、最近定义





自动装配

@SpringBootApplication 注解

官网文档：Spring Boot auto-configuration attempts to automatically configure your Spring application based on the jar dependencies that you have added. For example, if HSQLDB is on your classpath, and you have not manually configured any database connection beans, then Spring Boot auto-configures an in-memory database.

```java
/**
 * Indicates a {@link Configuration configuration} class that declares one or more
 * {@link Bean @Bean} methods and also triggers {@link EnableAutoConfiguration
 * auto-configuration} and {@link ComponentScan component scanning}. This is a convenience
 * annotation that is equivalent to declaring {@code @Configuration},
 * {@code @EnableAutoConfiguration} and {@code @ComponentScan}.
 *
 * @author Phillip Webb
 * @author Stephane Nicoll
 * @since 1.2.0
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
      @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

   /**
    * Exclude specific auto-configuration classes such that they will never be applied.
    * @return the classes to exclude
    */
   @AliasFor(annotation = EnableAutoConfiguration.class)
   Class<?>[] exclude() default {};

   /**
    * Exclude specific auto-configuration class names such that they will never be
    * applied.
    * @return the class names to exclude
    * @since 1.3.0
    */
   @AliasFor(annotation = EnableAutoConfiguration.class)
   String[] excludeName() default {};

   /**
    * Base packages to scan for annotated components. Use {@link #scanBasePackageClasses}
    * for a type-safe alternative to String-based package names.
    * @return base packages to scan
    * @since 1.3.0
    */
   @AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
   String[] scanBasePackages() default {};

   /**
    * Type-safe alternative to {@link #scanBasePackages} for specifying the packages to
    * scan for annotated components. The package of each class specified will be scanned.
    * <p>
    * Consider creating a special no-op marker class or interface in each package that
    * serves no purpose other than being referenced by this attribute.
    * @return base packages to scan
    * @since 1.3.0
    */
   @AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
   Class<?>[] scanBasePackageClasses() default {};

}
```







@SpringBootConfiguration 注解

多层次@Component 派生性

@Component

@Configuration

@SpringBootConfiguration

@Component 派生注解 ，能够被 @ComponentScan 扫描。包括 ：@Repository、@Service 、@Controller 注解，直接派生自 @Component

注解中的 “属性方法声明”。@AliasFor 注解。

@SpringBootApplication 标注非引导类。





自动配置类

关于 DataSourceAutoConfiguration 是如何装配的。

spring-boot-autoconfigure 是 spring-boot 的核心模块（MEAT-INF/spring.factories），它们统一存放在 org.springframework.boot.autoconfigure包或子包下。

# Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener

# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.autoconfigure.BackgroundPreinitializer

# Auto Configuration Import Listeners
org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener

# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
org.springframework.boot.autoconfigure.condition.OnClassCondition,\
org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\







理解 Production-Ready 特性

Production-Ready 是 DevOps 的立足点

1、health 
2、metrics
3、Tracing



关于Metrics, tracing, and logging  （http://peter.bourgon.org/blog/2017/02/21/metrics-tracing-and-logging.html， 中文版：http://blog.oneapm.com/apm-tech/811.html）



Actuator endpoints let you monitor and interact with your application。



Spring Boot Actuator 内建Endpoints


服务端资料 > Spring > 20191205151331.jpg



Spring Boot Actuator  支持很多的监控系统（包括主流商业产品和开源工具。）

比如支持elastic，支持的指标：

* JVM metrics, report utilization of:
* Various memory and buffer pools
* Statistics related to garbage collection
* Threads utilization
* Number of classes loaded/unloaded
* CPU metrics
* File descriptor metrics
* Kafka consumer metrics
* Log4j2 metrics: record the number of events logged to Log4j2 at each level
* Logback metrics: record the number of events logged to Logback at each level
* Uptime metrics: report a gauge for uptime and a fixed gauge representing the application’s absolute start time
* Tomcat metrics (server.tomcat.mbeanregistry.enabled must be set to true for all Tomcat metrics to be registered)







注解驱动

在Spring 5 中 引入了 spring-context-indexer 为了 提示启动性能。 在编译时生成 索引文件，启动过程中，CandidateComponentsIndexLoader会读取该文件，而不扫描指定的package，达到提升性能的目的。

使用了 Java  annotation Processor 插件化机制（SPI）。与lombok 实质类型。在编译过程中，修改编译结果。（ 后续再进一步了解）



@Enable  模块驱动 注解



注解在 Configuration类上面。关于注解的解析过程在org.springframework.context.annotation.ConfigurationClassPostProcessor（实现了PriorityOrdered，在初始化过程中，优先回调，主要解析bean定义和相关配置注解)。

关键解析过程在：org.springframework.context.annotation.ConfigurationClassParser#doProcessConfigurationClass



@Import   注解（注解在Configuration，@Enable 上）

其中value可以是@Configuration} classes,   ImportSelector和ImportBeanDefinitionRegistrar 接口的实现。



@EnableAutoConfiguration 注解 & 过程

```java
/**
 * Enable auto-configuration of the Spring Application Context, attempting to guess and
 * configure beans that you are likely to need. Auto-configuration classes are usually
 * applied based on your classpath and what beans you have defined. For example, if you
 * have {@code tomcat-embedded.jar} on your classpath you are likely to want a
 * {@link TomcatServletWebServerFactory} (unless you have defined your own
 * {@link ServletWebServerFactory} bean).
 * <p>
 * When using {@link SpringBootApplication @SpringBootApplication}, the auto-configuration
 * of the context is automatically enabled and adding this annotation has therefore no
 * additional effect.
 * <p>
 * Auto-configuration tries to be as intelligent as possible and will back-away as you
 * define more of your own configuration. You can always manually {@link #exclude()} any
 * configuration that you never want to apply (use {@link #excludeName()} if you don't
 * have access to them). You can also exclude them via the
 * {@code spring.autoconfigure.exclude} property. Auto-configuration is always applied
 * after user-defined beans have been registered.
 * <p>
 * The package of the class that is annotated with {@code @EnableAutoConfiguration},
 * usually via {@code @SpringBootApplication}, has specific significance and is often used
 * as a 'default'. For example, it will be used when scanning for {@code @Entity} classes.
 * It is generally recommended that you place {@code @EnableAutoConfiguration} (if you're
 * not using {@code @SpringBootApplication}) in a root package so that all sub-packages
 * and classes can be searched.
 * <p>
 * Auto-configuration classes are regular Spring {@link Configuration @Configuration}
 * beans. They are located using the {@link SpringFactoriesLoader} mechanism (keyed
 * against this class). Generally auto-configuration beans are
 * {@link Conditional @Conditional} beans (most often using
 * {@link ConditionalOnClass @ConditionalOnClass} and
 * {@link ConditionalOnMissingBean @ConditionalOnMissingBean} annotations).
 *
 * @author Phillip Webb
 * @author Stephane Nicoll
 * @see ConditionalOnBean
 * @see ConditionalOnMissingBean
 * @see ConditionalOnClass
 * @see AutoConfigureAfter
 * @see SpringBootApplication
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

   String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

   /**
    * Exclude specific auto-configuration classes such that they will never be applied.
    * @return the classes to exclude
    */
   Class<?>[] exclude() default {};

   /**
    * Exclude specific auto-configuration class names such that they will never be
    * applied.
    * @return the class names to exclude
    * @since 1.3.0
    */
   String[] excludeName() default {};

}
在 org.springframework.boot.autoconfigure.AutoConfigurationImportSelector 中，读取 META-INF/spring-autoconfigure-metadata.properties  中的配置，生成类名 数组。




```


问题：为什么应用自定义配置Class 能够覆盖其自动装配Class？

















条件化自动装配




注意（来自官方文档）：Auto-configurations must be loaded that way only. Make sure that they are defined in a specific package space and that they are never the target of component scanning. Furthermore, auto-configuration classes should not enable component scanning to find additional components. Specific @Imports should be used instead.



条件注解（org.springframework.boot.autoconfigure.condition）：



Class Conditions

Bean Conditions
Property Conditions
Resource Conditions
Web Application Conditions
SpEL Expression Conditions


在文档里有这么一句提示：

@ConditionalOnBean and @ConditionalOnMissingBean do not prevent @Configuration classes from being created. The only difference between using these conditions at the class level and marking each contained @Bean method with the annotation is that the former prevents registration of the @Configuration class as a bean if the condition does not match.
这个问题 ：（区别在于 Configuration  是否被注册 ）





问题：如果classpath没有相应的类，运行过程中，为啥注解中的.class引用不会报错？ 











初始化阶段

org.springframework.boot.SpringApplication#SpringApplication(org.springframework.core.io.ResourceLoader, java.lang.Class<?>...)



运行阶段

结束阶段



应用退出











Spring ApplicationContext 初始化过程







## Spring Cloud
