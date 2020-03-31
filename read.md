内容：
1 FileSystemXmlApplicationContext 加载过程

    AbstractRefreshableConfigApplicationContext 通过父类的方法进行对 Bean 定义资源文件的定位
    
    AbstractApplicationContext 关键类， 关键方法 refresh() 容器的起点 || obtainFreshBeanFactory() 方法调用子类容器的refreshBeanFactory()方法
    AbstractRefreshableApplicationContext 子类实现的 refreshBeanFactory()方法，
    AbstractXmlApplicationContext 实现了父类 AbstractRefreshableApplicationContext 的 loadBeanDefinitions 方法
    AbstractBeanDefinitionReader 读 取 Bean 定 义 资 源 , 
        在 其 抽 象 父 类AbstractBeanDefinitionReader 中定义了载入过程  
        调用资源加载器的获取资源方法 resourceLoader.getResource(location)，获取到要加载的资源。
        真正执行加载功能是其子类 XmlBeanDefinitionReader 的 loadBeanDefinitions 方法。
    
    XmlBeanDefinitionReader 通过调用其父类 DefaultResourceLoader 的 getResource 方法获取要加载的资源
    DefaultResourceLoader 中 的 getSource() 方 法 定 位 Resource
    FileSystemXmlApplicationContext 容器提供了 getResourceByPathh 方法的实现，就是为了处理既不是 classpath 标识，又不是 URL 标识的 Resource 定位这种情况。
    
    
    DefaultDocumentLoader 将 Bean 定义资源转换为 Document 对象
    XmlBeanDefinitionReader 对 Bean 定义的 Document 对象解析
    DefaultBeanDefinitionDocumentReader 解析document配置文件
    BeanDefinitionParserDelegate 解析 Bean 定义资源文件中的<bean>元素：
    BeanDefinitionReaderUtils.registerBeanDefinition IOC注入  对生成的bean进行注入操作   完成者：DefaultListableBeanFactory
    
    
    DefaultListableBeanFactory 向 IOC 容器注册解析后的 BeanDefinition： 使 用 一 个 HashMap 的 集 合 对 象 存 放 IOC 容 器 中 注 册 解 析 的BeanDefinition
    
    
    
2 AbstractBeanFactory依赖注入

    AbstractBeanFactory 实现了beanFactory  为用户提供IOCbean的管理
        统一定义了protected <T> T doGetBean(final String name, @Nullable final Class<T> requiredType,@Nullable final Object[] args, boolean typeCheckOnly) throws BeansException
        用于获取被管理的bean并进行依赖注入 
    AbstractAutowireCapableBeanFactory  完成bean实例的创建
        createBeanInstance 创建 Bean 的 java 实例对象
            SimpleInstantiationStrategy    使用初始化策略实例化 Bean 对象： 默认无参构造方法  两种方式如果 Bean 有方法被覆盖了，则使用 JDK 的反射机制进行实例化，否则，使用 CGLIB 进行实例化
                CglibSubclassingInstantiationStrategy 子类实现cglibi反射创建实例  
        populateBean 方法对 Bean 属性的依赖注入
            applyPropertyValues 解析并注入依赖属性的过程
                BeanDefinitionValueResolver.resolveValueIfNecessary 解析属性值   属性值需要进行类型转换时，如对其他对象的引用等，首先需要解析属性值，然后对解析后的属性值进行依赖注入
                BeanWrapperImpl =》由 AbstractPropertyAccessor 的子类  AbstractNestablePropertyAccessor 实现
                    AbstractNestablePropertyAccessor 是对容器中完成初始化的 Bean 实例对象进行属性的依赖注入