1. 新建工程

2. 解决没有web.xml导致的报错
  ```
  在pom.xml中加入配置
  <build>
  	<plugins>
  		<plugin>
  			<groupId>org.apache.maven.plugins</groupId>
  			<artifactId>maven-war-plugin</artifactId>
  			<version>2.4</version>
  			<configuration>
  				<failOnMissingWebXml>false</failOnMissingWebXml>
  			</configuration>
  		</plugin>
  	</plugins>
  </build>
  ```
3. 分析
 ```
1、web容器在启动的时候，会扫描每个jar包下的META-INF/services/javax.servlet.ServletContainerInitializer
2、加载这个文件指定的类SpringServletContainerInitializer
3、spring的应用一启动会加载感兴趣的WebApplicationInitializer接口的下的所有组件；
4、并且为WebApplicationInitializer组件创建对象（组件不是接口，不是抽象类）
	1）、AbstractContextLoaderInitializer：创建根容器；createRootApplicationContext()；
	2）、AbstractDispatcherServletInitializer：
			创建一个web的ioc容器；createServletApplicationContext();
			创建了DispatcherServlet；createDispatcherServlet()；
			将创建的DispatcherServlet添加到ServletContext中；
				getServletMappings();
	3）、AbstractAnnotationConfigDispatcherServletInitializer：注解方式配置的DispatcherServlet初始化器
			创建根容器：createRootApplicationContext()
					getRootConfigClasses();传入一个配置类
			创建web的ioc容器： createServletApplicationContext();
					获取配置类；getServletConfigClasses();
	
总结：
	以注解方式来启动SpringMVC；继承AbstractAnnotationConfigDispatcherServletInitializer；
实现抽象方法指定DispatcherServlet的配置信息； 
 ```
4. 实现
```
com.atguigu.MyWebAppInitializer
com.atguigu.config.AppConfig
com.atguigu.config.RootConfig
com.atguigu.controller.HelloController
com.atguigu.service.HelloService
```
