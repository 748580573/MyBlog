---
title: thymeleaf配置
date: 2018-07-09 09:57:44
categories: springs
tags: spring
---

## pom依赖

````xml
    <dependency>
      <groupId>org.thymeleaf</groupId>
      <artifactId>thymeleaf-spring4</artifactId>
      <version>2.1.4.RELEASE</version>
    </dependency>
````
````
注：我将该版本与spring 4.3.0进行整合
````

## xml配置

````xml
    <!--Thymeleaf视图解析器-->
    <bean id="viewResolver" class="org.thymeleaf.spring4.view.ThymeleafViewResolver">
        <property name="templateEngine" ref="templateEngine"/>
    </bean>

    <!--模板引擎 -->
    <bean id="templateEngine" class="org.thymeleaf.spring4.SpringTemplateEngine">
        <property name="templateResolver" ref="templateResolver"/>
    </bean>

    <!-- 模板解析器 -->
    <bean id="templateResolver" class="org.thymeleaf.templateresolver.ServletContextTemplateResolver">
        <property name="prefix" value="/view/"/>
        <property name="suffix" value=".html"/>
        <property name="templateMode" value="HTML5"/>
    </bean>

````

## html命名空间声明
````html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
````