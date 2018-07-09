---
title: 在xml中搭建spirng mvc
date: 2018-07-09 13:50:09
categories: springs
tags: spring
---

# 在web.xml中声明DispatcherServlet

如下是个一基本的web.xml文件，它按传统的方式搭建了DispatcherServlet和ContextLoaderListener。

````xml
	<!-- 配置spring核心监听器，默认会以 /WEB-INF/applicationContext.xml作为配置文件 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- contextConfigLocation参数用来指定Spring的配置文件 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/applicationContext*.xml</param-value>
	</context-param>


	<!-- 定义Spring MVC的前端控制器 -->
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/springmvc-config.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  
  <!-- 让Spring MVC的前端控制器拦截所有请求 -->
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  
````

在这里ContextLoaderListener和DispatcherServlet各自都会加载一个Spring应用上下文。上下文参数contextConfigLocation指定了一个xml文件的地址，这个文件定义了根应用上下文，它会被ContextLoaderListener加载。这里它会从"/WEB-INF/applicationContext*.xml"中加载bean定义。
DispatcherServlet会根据Servlet的名字找到一个文件，因此DispatcherServlet会从/WEB-INF/springmvc-Context.xml中加载应用上下文，但是这里我们指定了初始化参数contextConfigLocation，所以DispatcherServlet会根据Servlet的名字找到一个文件会从/WEB-INF/springmvc-config.xml文件加载上下文