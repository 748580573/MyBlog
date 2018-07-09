---
title: Spring 装配Bean
date: 2018-06-14 09:21:27
categories: springs
tags: spring
---

# 自动化装配bean

## 为组件扫描的bean命名
### @Component   

Spring应用上下文中所有bean都会给定一个ID,如果想给这个bean设置不同的Id,你所要做的就是将期望的ID作为值传递给@Component注解。



````java
      @Named("breakHeart")
      public class Myclass{
    
      }
```` 

### @Named

还有另外一种为bean命名的方式，这种方式使用java依赖注入规范中所提供@Named注解来为bean设置Id


````java
      @Named("breakHeart")
      public class Myclass{
    
      }
````

## 设置组件扫描的基础包
### @componentScan()


当@componentScan()没有设置任何属性时，这意味着，按照默认规则，它会以配置类所在的包作为基础包(base package)来扫描组件。


````java
      @Configuration
      @ComponentScan
      public class Myclass{
    
      }
```` 

但是，如果你想扫描不同的包，那该怎么办呢，要满足这样的需求其实也完全没有问题！为了指定不同的基础包，你所需要做的就是在@ComponetScan的value属性中指明包的名称
````java
      @Configuration
      @ComponentScan("com.pasckageName")
      public class Myclass{
    
}
```` 

如果你想更加清楚地表明你所设置的基础包，那么你可以通过basePackages属性进行配置

````java
    @Configuration
    @ComponentScan(basePackages = "com.packageName")
    public class Myclass{
    
}
````


可能你已经注意到了basePackages属性使用的是复数形式。如果你揣测这是不是意味着可以设置多个基础包，那么恭喜你猜对了。


````java
@Configuration
@ComponentScan(basePackages = {"com.packageName1，com.packageName2"})
public class Myclass{
    
}
````


除了将包设置为简单的String类型之外，@ComponentScan还提供了另外一种方法，那就是将其指定为包中所包含的类或接口


````java
      @Configuration
      @ComponentScan(basePackageClasses={{Test.class,Test2.class}})
      public class Myclass{
    
      }
````
## 创建配置类

创建JavaConfig类的关键在于为其添加@Configration注解，@Configration注解表明这个类是一个配置类，该类应该包含在Spring应用上下文中如何创建bean的细节

````java
@Configration
public class  MyConfig(){
    
}
````

### 声明简单的bean
要在JavaConfig中声明bean,我们需要编写一个方法，这个方法会创建所需类型的实例，然后给这个方法添加@Bean注解。

````java
@Bean
public CompactDisc sgtPeppers(){
    return new CompactDisc();
}
````

在本例中，bean的名字将会是setPeppers。如果你想为其设置成一个不同的名字的话，那么可以重命名该方法，也可以通过name指定一个不同的名字：

````java
@Bean(name="anotherName")
public CompactDisc sgtPeppers(){
    return new CompactDisc();
}
````
### 在JavaConfig中应用XML配置
在典型的Spring应用中，我们可能会同时使用自动化和显示配置。可以通过@Import()注解引用另外一个配置类，@ImportResource()应用xml配置

````java
@Configration
@Import(MyConfig.class)
@Import("classpath:test-config.xml")
public class SoundConfig(){
    
}
````