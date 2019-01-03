#### 注解实现servlet3.0
1. @webServlet

```
com.atguigu.servlet.HelloServlet
WebContent/index.jsp
```
2. HandlesTypes 

  - 容器启动的时候会将@HandlesTypes指定的这个类型下面的子类（实现类，子接口等）传递过来；
  
  ```
  META-INF/services/javax.servlet.ServletContainerInitializer
  com.atguigu.servlet.MyServletContainerInitializer
  com.atguigu.service.HelloService
  com.atguigu.service.AbstractHelloService
  com.atguigu.service.HelloServiceExt
  com.atguigu.service.HelloServiceImpl
  ```
3. 使用编码的方式，在项目启动的时候给ServletContext里面添加组件
```
com.atguigu.servlet.MyServletContainerInitializer
com.atguigu.servlet.UserFilter
com.atguigu.servlet.UserListener
com.atguigu.servlet.UserServlet
```
