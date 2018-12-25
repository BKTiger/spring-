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
	
1. 


				
