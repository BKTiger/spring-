## 学习顺序及重点笔记

#### 前期准备

- 版本说明
 
	4.3.12
 
- 导入jar包
 
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.12.RELEASE</version>
</dependency>
```
#### 老版开发方法
	
- 使用配置文件注入实体类 
    
    ```
    com.atguigu.bean.Person
    beans.xml
    
    ```
- 标签
	
    ```
    <!-- 包扫描、只要标注了@Controller、@Service、@Repository，@Component -->
    <context:component-scan base-package="com.atguigu" ></context:component-scan>
    ```
    - 测试类 com.atguigu.MainTest
	
#### 配置类开发
   
   ```
   com.atguigu.config.MainConfig
   ```
- @Configuration
   
  配置类的标识
   
- @Bean 
   
  在容器中注册Bean,返回值为注册类型
   
- @ComponentScan
- @ComponentScans:指定多个@ComponentScan
   
  value:指定要扫描的包
  
  excludeFilters:指定要排除扫描的包
  
  includeFilters:指定包含的包(使用此注解时应加入:useDefaultFilters = false，将默认的扫描方式关掉)
  
   ```
   写法样式:
   @ComponentScans(
		value = {
				@ComponentScan(value="com.atguigu",includeFilters = {
	/*					@Filter(type=FilterType.ANNOTATION,classes={Controller.class}),
						@Filter(type=FilterType.ASSIGNABLE_TYPE,classes={BookService.class}),*/
						@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class})
				},useDefaultFilters = false)	
		}
		)
		
   规则:
   @ComponentScan  value:指定要扫描的包
    excludeFilters = Filter[] ：指定扫描的时候按照什么规则排除那些组件
    includeFilters = Filter[] ：指定扫描的时候只需要包含哪些组件
        FilterType.ANNOTATION：按照注解
        FilterType.ASSIGNABLE_TYPE：按照给定的类型；
        FilterType.ASPECTJ：使用ASPECTJ表达式
        FilterType.REGEX：使用正则指定
        FilterType.CUSTOM：使用自定义规则 com.atguigu.config.MyTypeFilter
   ```

  测试类:com.atguigu.test.IOCTest#test01

#### 配置类的详细使用
 
	 ```
	   com.atguigu.config.MainConfig2
	   测试位置: com.atguigu.test.IOCTest#test02
	```
   
1. spring默认单实例,ioc容器启动会调用方法创造对象,以后获取就直接从容器拿
    
2. 使用 @Scope("prototype") 变为多实例,容器启动不会创造对象,只有在获取时才会创建
    
3. @Lazy 懒加载(容器启动不创建对象),在第一次使用时创建对象
	
    
	```
		测试位置: com.atguigu.test.IOCTest#test03
	```
    
4. @Conditional 按照一定条件进行判断,满足条件的在容器中航注册,不满足的不注册
	
	1. 传入condition数组
		
			```
			com.atguigu.condition.LinuxCondition
			com.atguigu.condition.WindowsCondition
			```
	2. 放在@Bean上边,则表示满足条件返回该注册的Bean;放在配置类上则表明只有满足条件才会返回该类下注册的Bean
		
        - 动态获取环境变量
		
			```
			ConfigurableEnvironment environment = applicationContext.getEnvironment();
			//动态获取环境变量的值；Windows 10
			String property = environment.getProperty("os.name");
			```
5. @import 

	```
	测试位置: com.atguigu.test.IOCTest#testImport
	```
		
6. 小结:给容器中注册组件的方法
	
	1. 包扫描+组件标注注解(@Controller/@Service/@Repository/@Component),名称默认类名首字母小写

	2. @Bean[导入第三方包里面的组件]

	3. @Import[快速给容器中导入一个组件]

		1. @import(要导入到容器种类的组件),id默认为全类名

		2. ImportSelector:返回需要导入组件的全类名数组

			```
			com.atguigu.condition.MyImportSelector
			```

		3. ImportBeanDefinitionRegistrar:手动注册bean

			```
			com.atguigu.condition.MyImportBeanDefinitionRegistrar
			```
	4. 使用spring提供的FactoryBean(工厂Bean)

			```
			com.atguigu.bean.ColorFactoryBean
			```

		1. 默认获取到的是工厂bean调用getObject创建的对象

		2. 要获取工厂Bean本身，我们需要给id前面加一个&
			&colorFactoryBean
#### bean的生命周期

- bean的声明周期指的是:bean的创建---初始化---销毁的过程,容器管理bean的生命周期,我们可以自定义初始化和销毁方法

	```
	配置类:com.atguigu.config.MainConfigOfLifeCycle
	试验实体类:com.atguigu.bean.Car
	测试:com.atguigu.test.IOCTest_LifeCycle
	```
1. 通过@Bean指定init-method和destroy-method

	- 单实例对象在容器创建时初始化,在容器关闭时销毁

	- 多实例对象在创建对象时初始化,容器不会管理

2. 通过让Bean实现InitializingBean（定义初始化逻辑），DisposableBean（定义销毁逻辑）;

	```
	com.atguigu.bean.Cat
	```
3. 可以使用JSR250注解;

	- @PostConstruct：在bean创建完成并且属性赋值完成；来执行初始化方法

	- @PreDestroy：在容器销毁bean之前通知我们进行清理工作

		```
		com.atguigu.bean.Dog
		```
4. BeanPostProcessor【interface】：bean的后置处理器；

	- 在bean初始化前后进行一些处理工作；

	- postProcessBeforeInitialization:在初始化之前工作

	- postProcessAfterInitialization:在初始化之后工作

	```
	com.atguigu.bean.MyBeanPostProcessor
	```
#### 属性赋值

```
配置类:com.atguigu.config.MainConfigOfPropertyValues
实体类:com.atguigu.bean.Person
测试类:com.atguigu.test.IOCTest_PropertyValue

```
	
1. @Value
	
	- 基本数值
	
	- 可以写 SpEL:#{} 例如:@Aalue("#{20-2}")
	
	- 可以写 #{},取出配置文件[properties]中的值(在运行环境变量中的值)
	
		1. @PropertySource 加载外部配置文件,读取文件文件中的K/V,保存在运行环境变量中:在配置类上加入@PropertySource(value = {"classpath:/person.properties"})

#### 自动装配

```
配置类:com.atguigu.config.MainConifgOfAutowired
测试类:com.atguigu.test.IOCTest_Autowired

```

1. 概念:Spring 利用依赖注入(DI),完成对IOC容器中各个组件的依赖关系赋值;

2. @Autowired 自动注入

	* 默认优先按照类型去容器中找对应的组件:applicationContext.getBean(Xxx.class)
	
	* 如果找到多个相同类型的组件,再将属性的名称作为组件的id去组件中查找:applicationContext.getBean("xxXxx")
3. @Qualifier("xxXxx")

	* 使用该注解指定需要装配的组件id
	
4. @Autowired(required = false)
	
	* 如果不指定required,默认一定要将属性赋值好,否则就会报错

5. @Primary:让Spring进行自动装配(Autowired)时.默认使用首选bean,也可以使用Qualifier("xxXxx")指定要使用的bean

6. @Resource

	* JSR250的规范
	
	* 默认根据组件名称进行装配,但是不会使用首选@Primary,没有支持required = false
	
	* @Resource(name = "xxXxx")指定bean
	
7. @Inject
	* JSR330的规范,需要导入jar包
		```
		<dependency>
			<groupId>javax.inject</groupId>
			<artifactId>javax.inject</artifactId>
			<version>1</version>
		</dependency>
		```
	* 和Autowired的功能一样,不过不能支持required = false
8. AutowiredAnnotationBeanPostProcessor:解析完成自动装配功能；

9. @Autowired:构造器,参数,方法,属性

	```
	com.atguigu.bean.Boss
	```
	
	1. 标注在方法
	
		* Spring容器创建当前对象，就会调用方法，完成赋值；
		
		* 方法使用的参数，自定义类型的值从ioc容器中获取
	
	2. 标注在构造器 
	
		* 默认加在ioc容器中的组件，容器启动会调用无参构造器创建对象，再进行初始化赋值等操作;
		
		* 构造器要用的组件，都是从容器中获取
		
		* 如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数位置的组件还是可以自动从容器中获取
		
	3. 放在参数位置
	
	4. @Bean标注的方法创建对象的时候，方法参数的值从容器中获取 默认不写Autowired

10. 自定义组件想要使用Spring容器底层的一些组件（ApplicationContext，BeanFactory，xxx）；

	```
	com.atguigu.bean.Red

	```

	* 自定义组件实现xxxAware；在创建对象的时候，会调用接口规定的方法注入相关组件；Aware；
	
	* 把Spring底层一些组件注入到自定义的Bean中；
	
	* xxxAware：功能使用xxxProcessor；
	
	* ApplicationContextAware==》ApplicationContextAwareProcessor；后置处理器实现Aware

11. @Profile

	```
	配置类:com.atguigu.config.MainConfigOfProfile
	测试类:com.atguigu.test.IOCTest_Profile
	```

	* 指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件
	
	1. 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
	
	2. 指定环境标识的方法
		
		* 使用命令行动态参数: 在虚拟机参数位置加载 -Dspring.profiles.active=test
		
		* 用代码方式
			```
			//1、创建一个applicationContext
			//2、设置需要激活的环境
			applicationContext.getEnvironment().setActiveProfiles("dev");
			//3、注册主配置类
			applicationContext.register(MainConfigOfProfile.class);
			//4、启动刷新容器
			applicationContext.refresh();
			```
				
	3. 写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效
#### AOP

- 原理:动态代理。指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式。

```
配置类:com.atguigu.config.MainConfigOfAOP
测试类:com.atguigu.test.IOCTest_AOP

```
1. 导入jar包

```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-aspects</artifactId>
	<version>4.3.12.RELEASE</version>
</dependency>
```

2. 业务逻辑类:com.atguigu.aop.MathCalculator:在业务逻辑运行的时候将日志进行打印,方法调用前后及出现bug时打印
	
3. 日志切面类:com.atguigu.aop.LogAspects 切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行；

	* 通知方法：
	
		* 前置通知(@Before)：logStart：在目标方法(div)运行之前运行
		
		* 后置通知(@After)：logEnd：在目标方法(div)运行结束之后运行（无论方法正常结束还是异常结束）
		
		* 返回通知(@AfterReturning)：logReturn：在目标方法(div)正常返回之后运行
		
		* 异常通知(@AfterThrowing)：logException：在目标方法(div)出现异常以后运行
		
		* 环绕通知(@Around)：动态代理，手动推进目标方法运行（joinPoint.procced()）
	* 通知写法写法:
		
		1. 直接在方法上加入注解@Before("public int com.atguigu.aop.MathCalculator.*(..)")
		
		2. 提取公共方法:

			```
			@Pointcut("execution(public int com.atguigu.aop.MathCalculator.*(..))")
			public void pointCut(){};

			//@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
			@Before("pointCut()")
			public void logStart(JoinPoint joinPoint){
				Object[] args = joinPoint.getArgs();
				System.out.println(""+joinPoint.getSignature().getName()+"运行。。。@Before:参数列表是：{"+Arrays.asList(args)+"}");
			}
			```
		
4. 将切面类和业务逻辑类（目标方法所在类）都加入到容器中;

5. 必须告诉Spring哪个类是切面类(给切面类上加一个注解：@Aspect)

6. 给配置类中加 @EnableAspectJAutoProxy 【开启基于注解的aop模式】

	* JoinPoint 获取方法的执行信息

7. 总结(3步)

	1. 将业务逻辑组件和切面类都加入到容器中；告诉Spring哪个是切面类（@Aspect）
	
	2. 在切面类上的每一个通知方法上标注通知注解，告诉Spring何时何地运行（切入点表达式）
	
	3. 开启基于注解的aop模式；@EnableAspectJAutoProxy
	
	
	
	
	
	
	
