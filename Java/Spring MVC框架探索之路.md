[jar包下载地址](http://repo.spring.io/release/org/springframework/spring/)

[spring-framework-4.0.6.RELEASE-dist](http://repo.spring.io/release/org/springframework/spring/4.0.6.RELEASE/)

[最小Spring Jar需求清单](./spring-4.0.6.rar)

控制器负责处理HTTP请求，调用服务层，组成响应对象，返回给请求工作流。像Servlet一样，作为单例，可以处理并发请求，不需要在每个请求中维持状态。

视图层的责任是智能展现和呈现模型中的数据对象。

控制器 AbstractController返回动态信息，但对输入并无响应。Controller受Spring容器管理，可运用依赖注入。任务方法                                                              
ModelAndView handleRequestInternal(HttpServletRequest,HttpServletResponse)

spring-servlet.xml
 
# 实际测试后的总结

总结下MyEclipse中使用Spring MVC框架开发应用搭建环境步骤：

    首先利用MyEclipse导入Spring框架所需jar包，包括Spring Core、Spring Web（MVC）。
搭建基本的bean容器环境，在web.xml中配置Spring框架的上下文加载监听器ContextLoaderListener。
同时，根据业务需要在applicationContext.xml中配置所需bean对象。

然后，开始搭建Spring Web MVC环境，在web.xml中配置核心任务路由Servlet对象DistpacherServlet，并指定路由器Servlet的虚拟路径，比如/app/*。

根据业务需要选择合适的Controller，比如AbstractController、SimpleFormController，继承这些类并腹写其中的核心方法，返回一个ModelAndView对象。接着，将其配置在spring-servlet.xml中，并连同ViewResolver一起配置。
两个核心配置文件都放在WEB-INF目录下，代码如下：

## spring-servlet.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE beans PUBLIC "//SPRING//DTD BEAN//E" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>
 <bean
  name="/home"
  class="com.spring.controller.HomeController">
  <property name="flightService" ref="flightService" />
 </bean>
 
 <bean
  id="viewResolver"
  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  <property name="prefix" value="/WEB-INF/jsp/"></property>
  <property name="suffix" value=".jsp"></property>
 </bean>
</beans>
```

## web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
 <listener>
  <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
 </listener>
 <servlet>
  <servlet-name>spring</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 </servlet>
 <servlet-mapping>
  <servlet-name>spring</servlet-name>
  <url-pattern>/app/*</url-pattern>
 </servlet-mapping>
 <welcome-file-list>
  <welcome-file>index.jsp</welcome-file>
 </welcome-file-list>
 <login-config>
  <auth-method>BASIC</auth-method>
 </login-config>
</web-app>
