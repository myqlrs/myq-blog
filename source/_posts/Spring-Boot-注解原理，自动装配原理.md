---
title: Spring Boot 注解原理，自动装配原理
date: 2021-12-08 21:27:05
tags:
 -Spring Boot
---

首先，先看SpringBoot的主配置类：

```java
@SpringBootApplication
public class StartEurekaApplication
{
    public static void main(String[] args)
    {
        SpringApplication.run(StartEurekaApplication.class, args);
    }
}
```

点进@SpringBootApplication来看，发现@SpringBootApplication是一个组合注解。

```java
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

}
```

首先我们先来看 @SpringBootConfiguration：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
}
```

可以看到这个注解除了元注解以外，就只有一个@Configuration，那也就是说这个注解相当于@Configuration，所以这两个注解作用是一样的，它让我们能够去注册一些额外的Bean，并且导入一些额外的配置。

那@Configuration还有一个作用就是把该类变成一个配置类，不需要额外的XML进行配置。所以@SpringBootConfiguration就相当于@Configuration。进入@Configuration，发现@Configuration核心是@Component，说明Spring的配置类也是Spring的一个组件。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";
}
```

继续来看下一个@EnableAutoConfiguration,这个注解是开启自动配置的功能。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

可以看到它是由 @AutoConfigurationPackage，@Import(EnableAutoConfigurationImportSelector.class)这两个而组成的，我们先说@AutoConfigurationPackage，他是说：让包中的类以及子包中的类能够被自动扫描到spring容器中。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
}
```

使用@Import来给Spring容器中导入一个组件 ，这里导入的是Registrar.class。来看下这个Registrar：

```java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
        Registrar() {
        }

        public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
            AutoConfigurationPackages.register(registry, (new AutoConfigurationPackages.PackageImport(metadata)).getPackageName());
        }

        public Set<Object> determineImports(AnnotationMetadata metadata) {
            return Collections.singleton(new AutoConfigurationPackages.PackageImport(metadata));
        }
    }
```

就是通过以上这个方法获取扫描的包路径，可以debug查看具体的值：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)img

那metadata是什么呢，可以看到是标注在@SpringBootApplication注解上的DemosbApplication，也就是我们的主配置类Application：

![图片](https://mmbiz.qpic.cn/mmbiz_png/JfTPiahTHJhpUFfrddvAHmB3bNFTu6akNesTib7YDsmxg53F5x8zchX4ibNibZPJ2qz5q8tV0R1ryF3D6DqG3X8b0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)img

其实就是将主配置类（即@SpringBootApplication标注的类）的所在包及子包里面所有组件扫描加载到Spring容器。因此我们要把DemoApplication放在项目的最高级中（最外层目录）。

看看注解@Import(AutoConfigurationImportSelector.class)，@Import注解就是给Spring容器中导入一些组件，这里传入了一个组件的选择器:AutoConfigurationImportSelector。

![图片](https://mmbiz.qpic.cn/mmbiz_png/JfTPiahTHJhpUFfrddvAHmB3bNFTu6akNIJ5ibA5Xx5sdhSMWtfLsfcib9W2PrbpJdHicqpiabHLExKgKcSymiakhN6Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)img

可以从图中看出AutoConfigurationImportSelector 继承了 DeferredImportSelector 继承了 ImportSelector，ImportSelector有一个方法为：selectImports。将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中。

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    } else {
        AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
        AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = 
        this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```

会给容器中导入非常多的自动配置类（xxxAutoConfiguration）；就是给容器中导入这个场景需要的所有组件，并配置好这些组件。

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)img

有了自动配置类，免去了我们手动编写配置注入功能组件等的工作。那是如何获取到这些配置类的呢，看看下面这个方法：

```java
protected AutoConfigurationImportSelector.AutoConfigurationEntry 
  getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    } else {
        AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
        List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
        configurations = this.removeDuplicates(configurations);
        Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
        this.checkExcludedClasses(configurations, exclusions);
        configurations.removeAll(exclusions);
        configurations = this.filter(configurations, autoConfigurationMetadata);
        this.fireAutoConfigurationImportEvents(configurations, exclusions);
        return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
    }
}
```

我们可以看到getCandidateConfigurations()这个方法，他的作用就是引入系统已经加载好的一些类，到底是那些类呢：

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, 
    "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
    String factoryClassName = factoryClass.getName();
    return (List)loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
}
```

会从META-INF/spring.factories中获取资源，然后通过Properties加载资源：

```java
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
    if (result != null) {
        return result;
    } else {
        try {
            Enumeration<URL> urls = classLoader != 
          null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
            LinkedMultiValueMap result = new LinkedMultiValueMap();

            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                Iterator var6 = properties.entrySet().iterator();

                while(var6.hasNext()) {
                    Map.Entry<?, ?> entry = (Map.Entry)var6.next();
                    String factoryClassName = ((String)entry.getKey()).trim();
                    String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                    int var10 = var9.length;

                    for(int var11 = 0; var11 < var10; ++var11) {
                        String factoryName = var9[var11];
                        result.add(factoryClassName, factoryName.trim());
                    }
                }
            }

            cache.put(classLoader, result);
            return result;
        } catch (IOException var13) {
            throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
        }
    }
}
```

可以知道SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作。以前我们需要自己配置的东西，自动配置类都帮我们完成了。如下图可以发现Spring常见的一些类已经自动导入。

![图片](https://mmbiz.qpic.cn/mmbiz_png/JfTPiahTHJhpUFfrddvAHmB3bNFTu6akNmnZSK7j5eqUa4O8cjCT0pbcLRN5F2lXIN2mlXYpjXdic676FlXASaAg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)img

接下来看@ComponentScan注解，@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class), @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })，这个注解就是扫描包，然后放入spring容器。

```java
@ComponentScan(excludeFilters = {
  @Filter(type = FilterType.CUSTOM,classes = {TypeExcludeFilter.class}), 
  @Filter(type = FilterType.CUSTOM,classes = {AutoConfigurationExcludeFilter.class})})
public @interface SpringBootApplication {}
```

总结下@SpringbootApplication：就是说，他已经把很多东西准备好，具体是否使用取决于我们的程序或者说配置。

接下来继续看run方法：

```java
public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
```

来看下在执行run方法到底有没有用到哪些自动配置的东西，我们点进run：

```java
public ConfigurableApplicationContext run(String... args) {
    //计时器
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    ConfigurableApplicationContext context = null;
    Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
    this.configureHeadlessProperty();
    //监听器
    SpringApplicationRunListeners listeners = this.getRunListeners(args);
    listeners.starting();

    Collection exceptionReporters;
    try {
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
        ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments);
        this.configureIgnoreBeanInfo(environment);
        Banner printedBanner = this.printBanner(environment);
        //准备上下文
        context = this.createApplicationContext();
        exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class,                       new Class[]{ConfigurableApplicationContext.class}, context);
        //预刷新context
        this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);
        //刷新context
        this.refreshContext(context);
        //刷新之后的context
        this.afterRefresh(context, applicationArguments);
        stopWatch.stop();
        if (this.logStartupInfo) {
            (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
        }

        listeners.started(context);
        this.callRunners(context, applicationArguments);
    } catch (Throwable var10) {
        this.handleRunFailure(context, var10, exceptionReporters, listeners);
        throw new IllegalStateException(var10);
    }

    try {
        listeners.running(context);
        return context;
    } catch (Throwable var9) {
        this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null);
        throw new IllegalStateException(var9);
    }
}
```

那我们关注的就是 refreshContext(context); 刷新context，我们点进来看。

```java
private void refreshContext(ConfigurableApplicationContext context) {
   refresh(context);
   if (this.registerShutdownHook) {
      try {
         context.registerShutdownHook();
      }
      catch (AccessControlException ex) {
         // Not allowed in some environments.
      }
   }
}
```

我们继续点进refresh(context);

```java
protected void refresh(ApplicationContext applicationContext) {
   Assert.isInstanceOf(AbstractApplicationContext.class, applicationContext);
   ((AbstractApplicationContext) applicationContext).refresh();
}
```

他会调用 ((AbstractApplicationContext) applicationContext).refresh();方法，我们点进来看：

```java
public void refresh() throws BeansException, IllegalStateException {
   synchronized (this.startupShutdownMonitor) {
      // Prepare this context for refreshing.
      prepareRefresh();
      // Tell the subclass to refresh the internal bean factory.
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
      // Prepare the bean factory for use in this context.
      prepareBeanFactory(beanFactory);

      try {
         // Allows post-processing of the bean factory in context subclasses.
         postProcessBeanFactory(beanFactory);
         // Invoke factory processors registered as beans in the context.
         invokeBeanFactoryPostProcessors(beanFactory);
         // Register bean processors that intercept bean creation.
         registerBeanPostProcessors(beanFactory);
         // Initialize message source for this context.
         initMessageSource();
         // Initialize event multicaster for this context.
         initApplicationEventMulticaster();
         // Initialize other special beans in specific context subclasses.
         onRefresh();
         // Check for listener beans and register them.
         registerListeners();
         // Instantiate all remaining (non-lazy-init) singletons.
         finishBeanFactoryInitialization(beanFactory);
         // Last step: publish corresponding event.
         finishRefresh();
      }catch (BeansException ex) {
         if (logger.isWarnEnabled()) {
            logger.warn("Exception encountered during context initialization - " +
                  "cancelling refresh attempt: " + ex);
         }
         // Destroy already created singletons to avoid dangling resources.
         destroyBeans();
         // Reset 'active' flag.
         cancelRefresh(ex);
         // Propagate exception to caller.
         throw ex;
      }finally {
         // Reset common introspection caches in Spring's core, since we
         // might not ever need metadata for singleton beans anymore...
         resetCommonCaches();
      }
   }
}
```

由此可知，就是一个spring的bean的加载过程。继续来看一个方法叫做 onRefresh()：

```java
protected void onRefresh() throws BeansException {
   // For subclasses: do nothing by default.
}
```

他在这里并没有直接实现，但是我们找他的具体实现：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)img

比如Tomcat跟web有关，我们可以看到有个ServletWebServerApplicationContext：

```java
@Override
protected void onRefresh() {
   super.onRefresh();
   try {
      createWebServer();
   }
   catch (Throwable ex) {
      throw new ApplicationContextException("Unable to start web server", ex);
   }
}
```

可以看到有一个createWebServer();方法他是创建web容器的，而Tomcat不就是web容器，那是如何创建的呢，我们继续看：

```java
private void createWebServer() {
   WebServer webServer = this.webServer;
   ServletContext servletContext = getServletContext();
   if (webServer == null && servletContext == null) {
      ServletWebServerFactory factory = getWebServerFactory();
      this.webServer = factory.getWebServer(getSelfInitializer());
   }
   else if (servletContext != null) {
      try {
         getSelfInitializer().onStartup(servletContext);
      }
      catch (ServletException ex) {
         throw new ApplicationContextException("Cannot initialize servlet context",
               ex);
      }
   }
   initPropertySources();
}
```

factory.getWebServer(getSelfInitializer());他是通过工厂的方式创建的。

```java
public interface ServletWebServerFactory {
   WebServer getWebServer(ServletContextInitializer... initializers);
}
```

可以看到 它是一个接口，为什么会是接口。因为我们不止是Tomcat一种web容器。

![图片](https://mmbiz.qpic.cn/mmbiz_png/JfTPiahTHJhpUFfrddvAHmB3bNFTu6akN9M0rdnqYTTrMViaNfYVgG0N2dXoM3xP1q8jyuBzRywLHkGlt0rf0e2w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)img

我们看到还有Jetty，那我们来看TomcatServletWebServerFactory：

```java
@Override
public WebServer getWebServer(ServletContextInitializer... initializers) {
   Tomcat tomcat = new Tomcat();
   File baseDir = (this.baseDirectory != null) ? this.baseDirectory
         : createTempDir("tomcat");
   tomcat.setBaseDir(baseDir.getAbsolutePath());
   Connector connector = new Connector(this.protocol);
   tomcat.getService().addConnector(connector);
   customizeConnector(connector);
   tomcat.setConnector(connector);
   tomcat.getHost().setAutoDeploy(false);
   configureEngine(tomcat.getEngine());
   for (Connector additionalConnector : this.additionalTomcatConnectors) {
      tomcat.getService().addConnector(additionalConnector);
   }
   prepareContext(tomcat.getHost(), initializers);
   return getTomcatWebServer(tomcat);
}
```

那这块代码，就是我们要寻找的内置Tomcat，在这个过程当中，我们可以看到创建Tomcat的一个流程。

如果不明白的话， 我们在用另一种方式来理解下，大家要应该都知道stater举点例子。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

首先自定义一个stater。

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.4.RELEASE</version>
    <relativePath/>
</parent>
<groupId>com.zgw</groupId>
<artifactId>gw-spring-boot-starter</artifactId>
<version>1.0-SNAPSHOT</version>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
    </dependency>
</dependencies>
```

我们先来看maven配置写入版本号，如果自定义一个stater的话必须依赖spring-boot-autoconfigure这个包,我们先看下项目目录。

![图片](https://mmbiz.qpic.cn/mmbiz_png/JfTPiahTHJhpUFfrddvAHmB3bNFTu6akNf5oUT4DeAQ5yvsNruIOibcJNpfBPVATous50srP31qdtpEZmN4VjTWg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)img

```java
public class GwServiceImpl  implements GwService{
    @Autowired
    GwProperties properties;

    @Override
    public void Hello()
    {
        String name=properties.getName();
        System.out.println(name+"说:你们好啊");
    }
}
```

我们做的就是通过配置文件来定制name这个是具体实现。

```java
@Component
@ConfigurationProperties(prefix = "spring.gwname")
public class GwProperties {

    String name="zgw";

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

这个类可以通过@ConfigurationProperties读取配置文件。

```java
@Configuration
@ConditionalOnClass(GwService.class)  //扫描类
@EnableConfigurationProperties(GwProperties.class) //让配置类生效
public class GwAutoConfiguration {

    /**
    * 功能描述 托管给spring
    * @author zgw
    * @return
    */
    @Bean
    @ConditionalOnMissingBean
    public GwService gwService()
    {
        return new GwServiceImpl();
    }
}
```

这个为配置类，为什么这么写因为，spring-boot的stater都是这么写的，我们可以参照他仿写stater，以达到自动配置的目的，然后我们在通过spring.factories也来进行配置。

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.gw.GwAutoConfiguration
```

然后这样一个简单的stater就完成了，然后可以进行maven的打包，在其他项目引入就可以使用。

[原文链接](https://mp.weixin.qq.com/s?__biz=MzIyMDI5MzA3NQ==&mid=2247501483&idx=1&sn=fce9ff281dc2ae21d8eae8ab399e0ef2&chksm=97ccb417a0bb3d018ef69aed04fa5926a5d0c8d51ff19da7e85e8581457824f6c9af0276ec7f&mpshare=1&scene=23&srcid=0419dhUwxPXdDWIm7nuqn1E3&sharer_sharetime=1638966291915&sharer_shareid=bc663c77380a462511f0cc754a6583d0#rd)



# 关于Spring注入方式的几道面试题

## **`@Autowired`, `@Resource`,  `@Inject` 三个注解的区别**

Spring 支持使用`@Autowired`, `@Resource`,  `@Inject` 三个注解进行依赖注入。下面来介绍一下这三个注解有什么区别。

### @Autowired

`@Autowired`为Spring 框架提供的注解，需要导入包`org.springframework.beans.factory.annotation.Autowired`。

这里先给出一个示例代码，方便讲解说明：

```java
public interface Svc {

    void sayHello();
}

@Service
public class SvcA implements Svc {

    @Override
    public void sayHello() {
        System.out.println("hello, this is service A");
    }

}

@Service
public class SvcB implements Svc {

    @Override
    public void sayHello() {
        System.out.println("hello, this is service B");
    }

}

@Service
public class SvcC implements Svc {

    @Override
    public void sayHello() {
        System.out.println("hello, this is service C");
    }
}
```

测试类：

```java
@SpringBootTest
public class SimpleTest {

    @Autowired
    // @Qualifier("svcA")
    Svc svc;

    @Test
    void rc() {
        Assertions.assertNotNull(svc);
        svc.sayHello();
    }

}
```

**装配顺序：**

1.按照`type`在上下文中查找匹配的bean，`查找type为Svc的bean`

2.如果有多个bean，则按照`name`进行匹配

- 如果有`@Qualifier`注解，则按照`@Qualifier`指定的`name`进行匹配，`查找name为svcA的bean`
- 如果没有，则按照变量名进行匹配，`查找name为svcA的bean`

3.匹配不到，则报错。（`@Autowired(required=false)`，如果设置`required`为`false`(默认为`true`)，则注入失败时不会抛出异常）

### @Inject

在Spring 的环境下，**`@Inject`和`@Autowired` 是相同的**，因为它们的依赖注入都是使用`AutowiredAnnotationBeanPostProcessor`来处理的。

![图片](https://mmbiz.qpic.cn/mmbiz/eQPyBffYbue8xkkL2NJymJeWyz1Z2TF6ZLTwnu4NLw9edjN8aBBecdulJJnuM4lQnuHLiaNHc7orF0kybh1XJmA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**`@Inject`是 JSR-330 定义的规范**，如果使用这种方式，切换到`Guice`也是可以的。

> Guice 是 google 开源的轻量级 DI 框架

如果硬要说两个的区别，首先`@Inject`是Java EE包里的，在SE环境需要单独引入。另一个区别在于`@Autowired`可以设置`required=false`而`@Inject`并没有这个属性。

### @Resource

`@Resource`是JSR-250定义的注解。Spring 在 `CommonAnnotationBeanPostProcessor`实现了对`JSR-250`的注解的处理，其中就包括`@Resource`。

![图片](https://mmbiz.qpic.cn/mmbiz/eQPyBffYbue8xkkL2NJymJeWyz1Z2TF6aXPsnoOibxrhTfHq0Kaw6zmIcrEzCJ1ohaRFDicW8kqLfmbFJA5pvLdw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

`@Resource`有两个重要的属性：`name`和`type`，而Spring 将`@Resource`注解的`name`属性解析为bean的名字，而`type`属性则解析为bean的类型。

**装配顺序：**

1. 如果同时指定了`name`和`type`，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。
2. 如果指定了`name`，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。
3. 如果指定了`type`，则从上下文中找到类型匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。
4. 如果既没有指定`name`，又没有指定`type`，则默认按照`byName`方式进行装配；如果没有匹配，按照`byType`h进行装配。

## **IDEA 提示 `Field injection is not recommended`**

在[使用IDEA](http://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247506835&idx=1&sn=ab279e4c7c4e5768bdc7873d14e5283a&chksm=ebd5e4bfdca26da9d5bc4268b59e98739ecb763b361e8f25426f440307c4e0820e4a7c470483&scene=21#wechat_redirect) 进行Spring 开发的时候，当你在字段上面使用`@Autowired`注解的时候，你会发现IDEA 会有警告提示：

> Field injection is not recommended
>
> Inspection info: Spring Team Recommends: "Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies".

![图片](https://mmbiz.qpic.cn/mmbiz/eQPyBffYbue8xkkL2NJymJeWyz1Z2TF6TRlXUlo8EoClMZy8MAUwc9y4suYUPkCnGic2UYegjDEnjcXGOBE0j0A/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

翻译过来就是这个意思：

> 不建议使用基于 field 的注入方式。
>
> Spring 开发团队建议：在你的Spring Bean 永远使用基于constructor 的方式进行依赖注入。对于必须的依赖，永远使用断言来确认。

比如如下代码：

```java
@Service
public class HelpService {
    @Autowired
    @Qualifier("svcB")
    private Svc svc;

    public void sayHello() {
        svc.sayHello();
    }
}

public interface Svc {
    void sayHello();
}

@Service
public class SvcB implements Svc {
    @Override
    public void sayHello() {
        System.out.println("hello, this is service B");
    }
}
```

将光标放到`@Autowired`处，使用`Alt + Enter` 快捷进行修改之后，代码就会变成基于Constructor的注入方式，**修改之后**：

```java
@Service
public class HelpService {
    private final Svc svc;
    
    @Autowired
    public HelpService(@Qualifier("svcB") Svc svc) {
        // Assert.notNull(svc, "svc must not be null");
        this.svc = svc;
    }
    
    public void sayHello() {
        svc.sayHello();
    }
}
```

如果按照Spring 团队的建议，如果`svc`是必须的依赖，应该使用`Assert.notNull(svc, "svc must not be null")`来确认。

修正这个警告提示固然简单，但是我觉得更重要是去理解为什么Spring 团队会提出这样的建议？直接使用这种基于 field 的注入方式有什么问题？

------

**首先我们需要知道，Spring 中有这么3种依赖注入的方式**：

- 基于 field 注入（属性注入）
- 基于 setter 注入
- 基于 constructor 注入（构造器注入）

### 1. 基于 field 注入

所谓基于 field 注入，就是在bean的变量上使用注解进行依赖注入。本质上是通过反射的方式直接注入到field。这是我平常开发中看的最多也是最熟悉的一种方式，同时，也正是 Spring 团队所不推荐的方式。比如：

```java
@Autowired
private Svc svc;
```

### 2. 基于 setter 方法注入

通过对应变量的`setXXX()`方法以及在方法上面使用注解，来完成依赖注入。比如：

```java
private Helper helper;

@Autowired
public void setHelper(Helper helper) {
    this.helper = helper;
}
```

> 注：在 `Spring 4.3` 及以后的版本中，setter 上面的 `@Autowired` 注解是可以不写的。

### 3. 基于 constructor 注入

将各个必需的依赖全部放在带有注解构造方法的参数中，并在构造方法中完成对应变量的初始化，这种方式，就是基于构造方法的注入。比如：

```java
private final Svc svc;
    
@Autowired
public HelpService(@Qualifier("svcB") Svc svc) {
    this.svc = svc;
}
```

> 在 `Spring 4.3` 及以后的版本中，如果这个类只有一个构造方法，那么这个构造方法上面也可以不写 `@Autowired` 注解。

#### 基于 field 注入的好处

正如你所见，这种方式非常的简洁，代码看起来很简单，通俗易懂。你的类可以专注于业务而不被依赖注入所污染。你只需要把`@Autowired`扔到变量之上就好了，不需要特殊的构造器或者set方法，依赖注入容器会提供你所需的依赖。

#### 基于 field 注入的坏处

> 成也萧何败也萧何

基于 field 注入虽然简单，但是却会引发很多的问题。这些问题在我平常开发阅读项目代码的时候就经常遇见。

**容易违背了单一职责原则**

使用这种基于 field 注入的方式，添加依赖是很简单的，就算你的类中有十几个依赖你可能都觉得没有什么问题，普通的开发者很可能会无意识地给一个类添加很多的依赖。

但是当使用构造器方式注入，到了某个特定的点，构造器中的参数变得太多以至于很明显地发现something is wrong。拥有太多的依赖通常意味着你的类要承担更多的责任，明显违背了单一职责原则（SRP：Single responsibility principle）。

> 这个问题在我司的项目代码真的很常见。

**依赖注入与容器本身耦合**

依赖注入框架的核心思想之一就是受容器管理的类不应该去依赖容器所使用的依赖。换句话说，这个类应该是一个简单的POJO(Plain Ordinary Java Object)能够被单独实例化并且你也能为它提供它所需的依赖。

这个问题具体可以表现在：

- 你的类和依赖容器强耦合，不能在容器外使用
- 你的类不能绕过反射（例如单元测试的时候）进行实例化，必须通过依赖容器才能实例化，这更像是集成测试

**不能使用属性注入的方式构建不可变对象(`final` 修饰的变量)**

#### Spring 开发团队的建议

> Since you can mix constructor-based and setter-based DI, it is a good rule of thumb to use constructors for mandatory dependencies and setter methods or configuration methods for optional dependencies.

简单来说，就是

- 强制依赖就用构造器方式
- 可选、可变的依赖就用setter 注入

当然你可以在同一个类中使用这两种方法。构造器注入更适合强制性的注入旨在不变性，Setter注入更适合可变性的注入。

让我们看看Spring 这样推荐的理由，首先是基于构造方法注入，

> The Spring team generally advocates constructor injection as it enables one to implement application components as immutable objects and to ensure that required dependencies are not null. Furthermore constructor-injected components are always returned to client (calling) code in a fully initialized state. As a side note, a large number of constructor arguments is a bad code smell, implying that the class likely has too many responsibilities and should be refactored to better address proper separation of concerns.

Spring 团队提倡使用基于构造方法的注入，因为这样一方面可以**将依赖注入到一个不可变的变量中 (注：`final` 修饰的变量)**，另一方面也可以**保证这些变量的值不会是 null**。此外，经过构造方法完成依赖注入的组件 (注：比如各个 `service`)，在被调用时可以**保证它们都完全准备好了**。与此同时，从代码质量的角度来看，**一个巨大的构造方法通常代表着出现了代码异味，这个类可能承担了过多的责任**。

而对于基于 setter 的注入，他们是这么说的：

> Setter injection should primarily only be used for optional dependencies that can be assigned reasonable default values within the class. Otherwise, not-null checks must be performed everywhere the code uses the dependency. One benefit of setter injection is that setter methods make objects of that class amenable to reconfiguration or re-injection later.

基于 setter 的注入，则只应该被用于注入非必需的依赖，同时在类中应该对这个依赖提供一个合理的默认值。如果使用 setter 注入必需的依赖，那么将会有过多的 null 检查充斥在代码中。**使用 setter 注入的一个优点是，这个依赖可以很方便的被改变或者重新注入**。