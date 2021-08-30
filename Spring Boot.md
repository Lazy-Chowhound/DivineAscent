[toc]

# *IOC*

​	*IOC*（控制反转）控制反转，它表示让容器管理对象，不用每次都自己取*new*对象

​	*Spring* *IOC*的思想是：*Spring*容器来实现这些相互依赖对象的创建、协调、管理和销毁工作，对象只需要关注业务逻辑本就可以了，而不是传统的在对象内部直接控制；也就可以被称之为：对象如何得到它的依赖对象的责任被反转了（*IOC*/*DI*）

 # *AOP*

​	*AOP* 主要实现的目的是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤或阶段，以获得逻辑过程中各部分之间低耦合性的隔离效果。其与设计模式完成的任务差不多，是提供另一种角度来思考程序的结构，来弥补面向对象编程的不足。例如：针对某些业务逻辑添加**日志、权限、事务**等功能。

​	*Spring* *AOP*(*Aspect* *Oriented* *Programming*，面向切面编程)是 *OOPs*(面向对象编程)的补充。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。对于其他类型的代码，如安全性、异常处理和透明的持续性也是如此。这种散布在各处的无关的代码被称为横切（*cross*-*cutting*）代码，在 *OOP* 设计中，它导致了大量代码的重复，而不利于各个模块的重用。

​	而 *AOP* 技术则恰恰相反，它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为*Aspect*，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护。

## 实现 *AOP* 的方式，

主要有两种

> + 采用动态代理技术，利用拦截方法的方式，对该方法进行装饰，以取代原有对象行为的执行；
>
> + 采用静态织入的方式，引入特定的语法创建"切面"，从而使得编译器可以在编译期间织入有关"切面"的代码。

但是最广泛使用的方式是*Spring* *AspectJ* 注解风格(*Spring* *AspectJ* *Annotation* *Style*)

## *AOP*相关概念

+ 连接点（*Joinpoint*）：程序执行的某个特定位置（如：某个方法调用前、调用后，方法抛出异常后）。*Spring*仅支持方法的连接点。

+ 切点（*Pointcut*）：切点是*Advice*所要*Weaving*的具体位置。切点相当于查询条件，一个切点可以对应多个连接点。

+ 增强（*Advice*）：增强是织入到目标类连接点上的一段程序代码。*Spring*提供的增强接口都是带方位名的，如：*BeforeAdvice*、*AfterReturningAdvice*、*ThrowsAdvice*等。

+ 引介（*Introduction*）：引介是一种特殊的增强，它为类添加一些属性和方法。这样，即使一个业务类原本没有实现某个接口，通过引介功能，可以动态的未该业务类添加接口的实现逻辑，让业务类成为这个接口的实现类。

+ 织入（*Weaving*）：织入是将 *Advice* 添加到目标类具体连接点上的过程，*AOP*有三种织入方式：①编译期织入：需要特殊的*Java*编译期（例如*AspectJ*的*ajc*）；②装载期织入：要求使用特殊的类加载器，在装载类的时候对类进行增强；③运行时织入：在运行时为目标类生成代理实现增强。*Spring*采用了动态代理的方式实现了运行时织入，而*AspectJ*采用了编译期织入和装载期织入的方式。

+ 切面（*Aspect*）：切面是由切点和 *Advice* 组成的，它包括了对横切关注功能的定义，也包括了对连接点的定义。

## *Spring AOP*和*AspectJ*对比

|                  *Spring* *AOP*                  |                          *AspectJ*                           |
| :----------------------------------------------: | :----------------------------------------------------------: |
|                在纯 *Java* 中实现                |                使用 *Java* 编程语言的扩展实现                |
|               不需要单独的编译过程               |      除非设置 *LTW*，否则需要 *AspectJ* 编译器 (*ajc*)       |
|                只能使用运行时织入                |       运行时织入不可用。支持编译时、编译后和加载时织入       |
|            功能不强-仅支持方法级编织             | 更强大 - 可以编织字段、方法、构造函数、静态初始值设定项、最终类/方法。 |
|    只能在由 *Spring* 容器管理的 *bean* 上实现    |                    可以在所有域对象上实现                    |
|               仅支持方法执行切入点               |                        支持所有切入点                        |
| 代理是由目标对象创建的, 并且切面应用在这些代理上 | 在执行应用程序之前 (在运行时) 前, 各方面直接在代码中进行织入 |
|               比 *AspectJ* 慢很多                |                          更好的性能                          |
|                  易于学习和应用                  |               相对于 *Spring* *AOP* 来说更复杂               |

# *Spring* *Boot* 的核心注解

启动类上面的注解是@*SpringBootApplication*，它也是 *Spring* *Boot* 的核心注解，

主要组合包含了以下 3 个注解：

> **@*SpringBootConfiguration*：组合了 *@Configuration* 注解，标记当前类为配置类。**
>
> ***@EnableAutoConfiguration*：打开自动配置的功能，也可以关闭某个自动配置的选项，**
>
> **如关闭数据源自动配置功能：*@SpringBootApplication(exclude{DataSourceAutoConfiguration.class})***
>
> ***@ComponentScan*：扫描*Bean*。**

# *Spring* *Boot* 自动配置原理是什么？

​	*@EnableAutoConfiguration* 为复合注解，其中最重要的是注解是*@Import(AutoConfigurationImportSelector.class)*，这个注解导入了*AutoConfigurationImportSelector*这个配置类，调用了*selectImports*→*getCandidateConfiguration*→ *SpringFactoriesLoader.loadFactoryNames()*方法得到待配置类名的集合，而这些配置类在*META-INF/spring.Factories*下，这些配置类为*xxxAutoConfiguration*，一般会有*@EnableConfigurationProperties*来导入相关属性的*Bean*，*@ConditionalOnProperties*，*@ConditonalOnClass*等限制条件，满足这些条件就可使该自动配置类生效，*@EnableConfigurationProperties*会导入对应的属性类，属性类一般是封装一些属性，注解*@ConfigurationProperties*从*yml*或*properties*文件中导入相关属性。

# *Spring* *Boot* 中的 *starter* 到底是什么**?**

​	*starter* 相当于封装好的配置类，结合上面提到的 *Spring* *Boot* 自动配置原理，引入对应的 *starter* 之后会引入对应的 *jar* 包，*jar* 包中就会有对应的 *AutoConfiguration* 类，*Spring Boot* 启动后会扫描 *jar* 包，*@EnableAutoConfiguration* 就可以加载对应的配置类和对应的 *Bean*，就可以获得相关的配置。

# *Spring*循环依赖是怎么解决的

## 循环依赖

当我们注入一个对象 *A* 时，需要注入对象 *A* 中某些属性，这些属性也就是对象 *A* 的依赖，把对象 *A* 中的依赖都初始化完成，对象*A*才算是创建成功。那么，如果对象*A*中有个属性是对象 *B*，而且对象 *B* 中有个属性是对象 *A*，那么对象 *A* 和对象 *B* 就算是循环依赖，如果不加处理，就会出现：创建对象 *A*-->处理 *A* 的依赖 *B* -->创建对象 *B* -->处理 *B* 的对象*A*-->创建对象 *A* -->处理 *A* 的依赖 *B*-->创建对象 *B*.....这样无限的循环。

## 基本思路

虽说要初始化一个 *Bean*，必须要注入*Bean*里的依赖，才算初始化成功，但并不要求此时依赖的依赖也都注入成功，只要依赖对象的构造方法执行完了，这个依赖对象就算存在了，注入就算成功了，至于依赖的依赖，之后再初始化也来得及。

因此，我们初始化一个 *Bean* 时，先调用 *Bean* 的构造方法，这个对象就在内存中存在了（对象里面的依赖还没有被注入），然后把这个对象保存下来，当循环依赖产生时，直接拿到之前保存的对象，于是循环依赖就被终止了，依赖注入也就顺利完成了。

比如：

>  假设对象 *A* 中有属性是对象 *B*，对象 *B* 中也有属性是对象 *A*，即 *A* 和 *B* 循环依赖。
>
> 创建对象 *A*，调用 *A* 的构造，并把 *A* 保存下来。
>
> 然后准备注入对象 *A* 中的依赖，发现对象 *A* 依赖对象 *B*，那么开始创建对象 *B*。
>
> 调用 *B* 的构造，并把 *B* 保存下来。
>
> 然后准备注入 *B* 的构造，发现 *B* 依赖对象 *A*，对象 *A* 之前已经创建了，直接获取 *A* 并把 *A* 注入 *B*（注意此时的对象 *A* 还没有完全注入成功，对象 *A* 中的对象 *B* 还没有注入），于是 *B* 创建成功。
>
> 把创建成功的 *B* 注入 *A*，于是 *A* 也创建成功了。
>
> 于是循环依赖就被解决了。

## 原理

解决循环依赖关键在于三层列表

>*singletonFactories*
>
>*earlySingletonObjects*
>
>*singletonObjects*

| 步骤 | 操作                                                         | 三层列表中的内容                                             |
| ---- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1    | 开始初始化对象 *A*                                           | *singletonFactories：<br>earlySingletonObjects：<br>singletonObjects：* |
| 2    | 调用 *A* 的构造，把*A*放入 *singletonFactories*              | *singletonFactories：A<br>earlySingletonObjects：<br>singletonObjects：* |
| 3    | 开始注入 *A* 的依赖，发现 *A* 依赖对象 *B*                   | *singletonFactories：A<br>earlySingletonObjects：<br>singletonObjects：* |
| 4    | 开始初始化对象 *B*                                           | *singletonFactories：A,B<br>earlySingletonObjects：<br>singletonObjects：* |
| 5    | 调用 *B* 的构造，把*B*放入 *singletonFactories*              | *singletonFactories：A,B<br>earlySingletonObjects：<br>singletonObjects：* |
| 6    | 开始注入 *B* 的依赖，发现 *B* 依赖对象 *A*                   | *singletonFactories：A,B<br>earlySingletonObjects：<br>singletonObjects：* |
| 7    | 开始初始化对象 *A*，发现 *A* 在 *singletonFactories* 里有，则直接获取 *A* | *singletonFactories：B<br>earlySingletonObjects：A<br>singletonObjects：* |
| 8    | 把*A*放入 *earlySingletonObjects* ，把 *A* 从 *singletonFactories* 删除，对象 *B* 的依赖注入完成 | *singletonFactories：B <br>earlySingletonObjects：A<br>singletonObjects：* |
| 9    | 对象*B*创建完成，把*B*放入 *singletonObjects* ，把*B*从 *earlySingletonObjects* 和*singletonFactories* 中删除 | *singletonFactories：<br>earlySingletonObjects：A<br>singletonObjects：B* |
| 10   | 对象 *B* 注入给 *A*，继续注入 *A* 的其他依赖，直到 *A* 注入完成 | *singletonFactories：<br>earlySingletonObjects：A<br>singletonObjects：B* |
| 11   | 对象*A*创建完成，把 *A* 放入 *singletonObjects*，把 *A* 从 *earlySingletonObjects* 和*singletonFactories* 中删除 | *singletonFactories：<br>earlySingletonObjects：<br>singletonObjects：A,B* |
| 12   | 循环依赖处理结束，*A* 和 *B* 都初始化和注入完成              | *singletonFactories：<br>earlySingletonObjects：singletonObjects：A,B* |

# *Spring* *Bean*的生命周期

## 实例化        

>启动*spring*容器,也就是创建*BeanFactory*, 加载之后,*spring*容器会将配置信息封装成*BeanDefinition*对象。*BeanDefitionRegistry*将*BeanDefinition*注册到 *DefaultListableBeanFactory*（*BeanFactory*）， 保存在它的一个*ConcurrentHashMap*。实例化前执行*InstantiationAwareBeanPostProcessor*的*postProcessBeforeInstantiation*，执行*bean*的构造方法对*bean*进行实例化，实例化完成之后执行*postProcessAfterInstantiation*

 ## 注入属性       

> 实例化之后，注入属性调用*BeanNameAware*的*setBeanName*方法，调用*BeanFactoryAware*的*setBeanFactory*方法，调用*ApplicationContextAware*的*setApplicationContext*方法

 ## 初始化     

> 初始化前调用*BeanPostProcessor*的*postProcessBeforeInitialization*，所有属性赋初始化值之后调用*InitializingBean*的*afterPropertiesSet*()方法，执行自定义的*init*-*method*或@*PostConstruct*方法，调用*BeanPostProcessor*的*postProcessAfterInitialization*

## 销毁      

>  销毁容器，调用*disposibleBean*的*destroy*方法，然后调用指定的*destroy*-*method*方法

# *Spring* 、*SpringMVC*、*SpringBoot*、*SpringCloud*

> + *Spring* 是一个一站式的轻量级的 *java* 开发框架，核心是控制反转（*IOC*）和面向切面（*AOP*），针对于开发的 *WEB* 层(*springMvc*)、业务层(*Ioc*)、持久层(*jdbcTemplate*)等都提供了多种配置解决方案；
>
> + *SpringMVC*是 *Spring* 基础之上的一个 *MVC* 框架，通过 *XML* 配置，统一开发前端视图和后端逻辑
>
> + *SpringBoot* 框架相对于 *SpringMvc* 框架来说，更专注于开发微服务后台接口，不开发前端视图，同时遵循默认优于配置，简化了插件配置流程，不需要配置 *xml*，相对 *SpringMVC*，大大简化了配置流程；
>
> + *Spring* *Cloud* 大部分的功能插件都是基于 *SpringBoot* 去实现的，*SpringCloud* 关注于全局的微服务整合和管理，将多个*SpringBoot* 单体微服务进行整合以及管理；*SpringCloud* 依赖于*SpringBoot*开发，而 *SpringBoot* 可以独立开发；

# *Spring* *Boot*请求过程

​	所有请求都会被*tomcat*容器交到 *dispatchServlet* 的 *doService* 方法中去处理，然后执行了 *doDispatch* 方法，*doDispatch* 方法首先调用 *getHandler* 找到 *url* 匹配的 *handler* 方法，然后调用*handle*() 来获得处理结果。

​	*getHandler* 方法是通过 *HandlerMapping* 接口来处理请求到对象映射。

​	*RequestMappingHandlerMapping*的*detectHandler*会寻找*handler*方法，然后注册到*map*里。（寻找所有被@*Controller*注解的类中被@*RequestMapping*注解的方法对象，然后解析方法对象的@*RequestMapping*注解，把解析结果封装成*RequestMappingInfo*对象，也就是说*RequestMappingInfo*对象是用来装载请求处理方法的配置信息的，以*url*为*key*，*RequestMappingInfo*为*value*存到*map*里，再以*RequestMappingInfo*为*key*，*hanlderMethod*为*value*存到*map*里）

​	匹配到了 *handler* 方法之后，就到了 *handler* 方法的调用，最终的实现是在 *InvocableHandlerMethod* 和 *ServletInvocableHandlerMethod* 中实现的，分别实现了对参数和返回值的处理。以参数解析为器例子，*springboot* 实现了一个 *Composite* 对象，其中有一个 *List* 和一个 *Map* 来分别存储所有的扫描到的参数解析器以及曾经匹配过的解析器缓存。外部调用 *resolveArgument* 方法来进行解析，这个方法首先会调用 *composite* 的私有方法 *getArgumentResolver* 来获取参数解析器，然后再用参数解析器进行解析。获取解析器的过程首先是从缓存*Map*中去取，如果没有取到（也就是之前该种参数解析器被缓存在*map*中），则会去循环注册的所有解析器的 *List*，找出第一个可以解析当前请求参数的解析器，进行参数解析，并把它缓存。

# *Spring*自动装配的方式

> 1. *byName*
>
> 2. *byType*
>
> 3. 构造函数
>
> 4. *autodetect* 首先容器尝试通过构造函数使用 *autowire* 装配，如果不能，则尝试通过 *byType* 自动装配。

 ## 自动装配的缺点

> 1. 无法对*int* *double*等基本类型和*String*等类型自动装配
> 2. 自动装配没有自定义装配精确

# *Spring*事务传播属性

​	待补充......
