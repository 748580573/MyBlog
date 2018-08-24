---
title: spring-boot文档
date: 2018-06-20 09:45:47
categories: spring
tags: spring
---
# Spring-boot 参考文档

## 入门
### 安装spring
#### maven 安装

<b>Spring Boot与Apache Maven 3.2或更高版本兼容。 如果您尚未安装Maven，则可以按照maven.apache.org上的说明进行操作。</b>
<b>Spring Boot依赖使用org.springframework.boot groupId。 通常，您的Maven POM文件将从spring-boot-starter-parent项目继承，并将依赖项声明为一个或多个“Starter”。 Spring Boot还提供了一个可选的Maven插件来创建可执行的jar。</b>

````xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.4.1.RELEASE</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
````
### 开发你的第一个Spring Boot应用程序
#### 创建pom
<b>我们需要从创建一个Maven pom.xml文件开始。 pom.xml是用来构建项目的秘诀。 打开您最喜欢的文本编辑器并添加以下内容：</b>

````xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.4.1.RELEASE</version>
    </parent>

    <!-- Additional lines to be added here... -->

</project>
````

<b>这应该给你一个工作版本，你可以通过运行mvn包来测试它（你下载可以忽略“ “jar will be empty - no content was marked for inclusion!”警告）。</b>

#### 添加classpath依赖

<b>Spring Boot提供了许多“Starters”，可以方便地将jar添加到你的classpath中。 我们的示例应用程序已经在POM的父节点中使用了spring-boot-starter-parent。 spring-boot-starter-parent是一个特别的启动器，它提供了有用的Maven默认值。 它还提供了一个dependency-management部分，以便您可以省略“blessed”依赖项的版本标签。</b>
<b>其他的“Starters”只是提供开发特定类型应用程序时可能需要的依赖关系。 由于我们正在开发一个Web应用程序，因此我们将添加一个spring-boot-starter-web依赖项 - 但在此之前，让我们看看我们目前拥有的。</b>

````
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
````

<b>mvn dependency:tree 命令打印项目依赖关系的树形表示。 你可以看到spring-boot-starter-parent本身不提供依赖关系。 让我们编辑我们的pom.xml，并在父节的下面添加spring-boot-starter-web依赖项</b>
````
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
````

<b>如果你再次运行mvn dependency：tree，你会看到现在有很多附加依赖项，包括Tomcat web服务器和Spring Boot本身。</b>

#### 编写代码

<b>为了完成我们的应用程序，我们需要创建一个Java文件。 Maven默认会从src / main / java编译源代码，因此您需要创建该文件夹结构，然后添加一个名为src / main / java / Example.java的文件：</b>

````java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.stereotype.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Example.class, args);
    }

}
````
#### @RestController and @RequestMapping annotations

<b>我们的Example类的第一个注释是@RestController。 这被称为固定注释。 它为阅读代码的人提供了线索，对于Spring来说，该类扮演着特定的角色。 在这种情况下，我们的类是一个web @Controller，所以Spring在处理传入的Web请求时会考虑它。</b>
<b>@RequestMapping注释提供了“路由”信息。 它告诉Spring，任何具有路径“/”的HTTP请求都应映射到home方法。 @RestController注释告诉Spring将结果字符串直接呈现给调用者。</b>
<b>注：@RestController是@Controller与@ResponseBody的结合

#### @EnableAutoConfiguration 注释

<b>第二个类级注释是@EnableAutoConfiguration。 这个注释告诉Spring Boot基于你添加的jar依赖关系来“猜测”你将如何配置Spring。 由于spring-boot-starter-web添加了Tomcat和Spring MVC，因此自动配置将假定您正在开发Web应用程序并相应地设置Spring。</b>

````
Starters 和 自动配置

自动配置旨在与“Starter”配合使用，但这两个概念并不直接相关。 您可以自由选择和选择初学者之外的jar依赖项，并且Spring Boot将尽其所能自动配置您的应用程序。
````

#### main方法

<b>我们应用程序的最后一部分是main()方法。 这只是一个遵循Java约定的应用程序入口点的标准方法。 我们的主要方法通过调用run来委托Spring Boot的SpringApplication类。 SpringApplication将引导我们的应用程序，从Spring开始，它将启动自动配置的Tomcat Web服务器。 我们需要将Example.class作为参数传递给run方法，以告知SpringApplication是Spring的主要组件。 args数组也被传递以暴露任何命令行参数。</b>

#### 运行example

<b>此时我们的应用程序应该可以工作。 由于我们使用了spring-boot-starter-parent POM，因此我们有一个有用的运行目标，我们可以使用它来启动应用程序。 键入mvn spring-boot：从根项目目录运行以启动应用程序：</b>

````
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.4.1.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)
````

<b>If you open a web browser to localhost:8080 you should see the following output:</b>

````
Hello World!
````
<b>要正常退出应用程序，请按ctrl-c。</b>

#### 创建一个可执行的jar

<b>让我们通过创建一个完全独立的可执行jar文件来完成我们的示例，该文件可以在生产环境中运行。 可执行jar（有时称为“fat jars”）是包含您的编译类以及您的代码需要运行的所有jar依赖项的归档文件。</b>
````

685/5000
可执行的jar和Java

Java不提供任何标准的方法来加载嵌套的jar文件（即它们本身包含在jar中的jar文件）。 如果您想发布独立的应用程序，这可能会是一个问题。

为了解决这个问题，许多开发者使用“uber” jars。 “uber” jars简单的从所有的的jars包的所有classes打包进一个单独的存档。 这种方法的问题在于，很难在应用程序中看到实
际使用的库。 如果在多个"user" jar使用相同的文件名（但是具有不同的内容），则它也可能是有问题的。

Spring Boot采用了不同的方法，可以让您直接嵌入jars。
````

<b>要创建一个可执行的jar，我们需要将spring-boot-maven-plugin添加到我们的pom.xml中。 在依赖关系部分的下面插入以下几行：</b>
````xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
````

<b>spring-boot-starter-parent POM包含<executions>配置来绑定重新打包目标。 如果您不使用父POM，则需要自行声明此配置。有关[详细信息](https://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/maven-plugin/usage.html)，请参阅插件文档。</b>

<b>保存你的pom.xml并从命令行运行 mvn package</b>
````
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:1.4.1.RELEASE:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
````

<b>如果您查看目标目录，则应该看到myproject-0.0.1-SNAPSHOT.jar。 该文件大小应该在10 Mb左右。 如果你想窥视它，你可以使用jar tvf：</b>
<b>要运行该应用程序，请使用java -jar命令：</b>

````
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.4.1.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
````

<b>和以前一样，要正常地退出应用程序，点击ctrl-c。</b>

## 使用Spring boot

<b>本节将详细介绍如何使用Spring Boot。 它涵盖了构建系统，自动配置以及如何运行应用程序等主题。 我们还介绍了一些Spring Boot的最佳实践。 虽然Spring Boot没有什么特别的特殊之处（它只是你可以使用的另一个库），但有一些建议，如果遵循这些建议，会使开发过程变得更容易一些。</b>

### 构建系统

<b>强烈建议您选择支持依赖关系管理的构建系统，并且可以使用发布到“Maven Central”存储库的构件。 我们建议您选择Maven或Gradle。 Spring Boot可以与其他构建系统（例如Ant）一起工作，但它们不会得到特别好的支持。</b>

### 依赖管理
<b>Spring Boot的每个版本都提供了它支持的依赖的列表。 在实践中，您不需要为构建配置中的任何这些依赖项提供版本，因为Spring Boot正在为您进行管理。 当您升级Spring Boot本身时，这些依赖关系也将以一致的方式升级。</b>
<b>注：如果您觉得这是必要的，您仍然可以指定一个版本并覆盖Spring Boot的建议。</b>

## Maven

<b>Maven用户可以继承spring-boot-starter-parent项目以获得合理的默认值。 父项目提供以下功能：</b>
<li><b>Java 1.6作为默认的编译器级别。</b></li>
<li><b>UTF-8源码编码。</b></li>
<li><b>依赖管理部分，允许您省略<version>标签，并从spring-boot-dependencies POM集成该标签。</b></li>
<li><b>合理的资源过滤</b></li>
<li><b>合理的插件配置(exec plugin, surefire, Git commit ID, shade).</b></li>
<li><b>为application.properties和application.yml包括特定的配置文件过滤合理的的资源（例如application-foo.properties和application-foo.yml）

<b>最后一点：由于默认配置文件接受Spring样式占位符（$ {...}），Maven过滤被更改为使用@ .. @占位符（您可以使用Maven属性resource.delimiter覆盖该占位符）。</b>

### 继承父starter 
````xml
<!-- Inherit defaults from Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.1.RELEASE</version>
</parent>
````

<b>您只需要在此依赖项上指定Spring Boot版本号。 如果您导入额外的starter，则可以安全地省略版本号。</b>
<b>通过该设置，您还可以通过覆盖自己项目中的属性来覆盖各个依赖项。 例如，要升级到另一个Spring Data发行版，您需要将以下内容添加到您的pom.xml中。</b>
````xml
<properties>
    <spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>
````

### 使用没有父POM的Spring Boot

<b>不是每个人都喜欢从spring-boot-starter-parent POM继承。 您可能需要使用您自己的公司标准父项，或者您可能更愿意明确声明所有Maven配置。<b>
   
<b>如果你不想使用spring-boot-starter-parent，你仍然可以通过使用scope = import dependency来保持依赖管理的好处（但不是插件管理）：</b>
````xml
<dependencyManagement>
     <dependencies>
        <dependency>
            <!-- Import dependency management from Spring Boot -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>1.4.1.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
````

<b>如上所述，该设置不允许您使用属性覆盖单个依赖项。 为了达到相同的结果，你需要在spring-boot-dependencies条目之前在项目的dependencyManagement中添加一个条目。 例如，要升级到另一个Spring Data发行版，您需要将以下内容添加到您的pom.xml中。</b>

````xml
<dependencyManagement>
    <dependencies>
        <!-- Override Spring Data release train provided by Spring Boot -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-releasetrain</artifactId>
            <version>Fowler-SR2</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>1.4.1.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
````

### 更改Java版本

<b>spring-boot-starter-parent选择相当保守的Java兼容性。 如果您想遵循我们的建议并使用较新的Java版本，则可以添加一个java.version属性：</b>

````xml
<properties>
    <java.version>1.8</java.version>
</properties>
````

### 使用Spring Boot Maven plugin

````xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
````

### Starters
<b>Starters一套方便的依赖描述符，可以包含在应用程序中。 您可以获得所需的所有Spring及相关技术的一站式服务，而无需搜索示例代码并复制粘贴依赖关系描述符。 例如，如果您想开始使用Spring和JPA来访问数据库，只需在项目中包含spring-boot-starter-data-jpa依赖项</b>
<b>Starters包含很多您需要快速启动并快速运行和一组支持的可传递依赖关系的依赖项项目，。
<b>以下应用程序starter由Spring Boot在org.springframework.boot组下提供：</b>


<div class="table">
<a name="d0e1550" href="#d0e1550"></a>
<p class="title">
<b>Table&nbsp;13.1.&nbsp;Spring Boot application starters</b>
</p>
<div class="table-contents">
<table summary="Spring Boot application starters" style="border-collapse: collapse;border-top: 0.5pt solid ; border-bottom: 0.5pt solid ; border-left: 0.5pt solid ; border-right: 0.5pt solid ; "><colgroup><col class="col_1"><col class="col_2"><col class="col_3"></colgroup><thead><tr><th style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top">Name</th><th style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top">Description</th><th style="border-bottom: 0.5pt solid ; " align="left" valign="top">Pom</th></tr></thead><tbody><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-thymeleaf" href="#spring-boot-starter-thymeleaf"></a><code class="literal">spring-boot-starter-thymeleaf</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building MVC web applications using Thymeleaf views</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-thymeleaf/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-ws" href="#spring-boot-starter-ws"></a><code class="literal">spring-boot-starter-ws</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Web Services. Deprecated as of 1.4 in favor of <a class="link" href="#spring-boot-starter-web-services"><code class="literal">spring-boot-starter-web-services</code></a></p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-ws/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-couchbase" href="#spring-boot-starter-data-couchbase"></a><code class="literal">spring-boot-starter-data-couchbase</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Couchbase document-oriented database and Spring Data Couchbase</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-couchbase/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-artemis" href="#spring-boot-starter-artemis"></a><code class="literal">spring-boot-starter-artemis</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for JMS messaging using Apache Artemis</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-artemis/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-web-services" href="#spring-boot-starter-web-services"></a><code class="literal">spring-boot-starter-web-services</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Web Services</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-web-services/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-mail" href="#spring-boot-starter-mail"></a><code class="literal">spring-boot-starter-mail</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Java Mail and Spring Framework’s email sending support</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-mail/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-redis" href="#spring-boot-starter-data-redis"></a><code class="literal">spring-boot-starter-data-redis</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Redis key-value data store with Spring Data Redis and the Jedis client</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-redis/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-web" href="#spring-boot-starter-web"></a><code class="literal">spring-boot-starter-web</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building web, including RESTful, applications using Spring MVC. Uses Tomcat as the default embedded container</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-web/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-gemfire" href="#spring-boot-starter-data-gemfire"></a><code class="literal">spring-boot-starter-data-gemfire</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using GemFire distributed data store and Spring Data GemFire</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-gemfire/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-activemq" href="#spring-boot-starter-activemq"></a><code class="literal">spring-boot-starter-activemq</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for JMS messaging using Apache ActiveMQ</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-activemq/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-elasticsearch" href="#spring-boot-starter-data-elasticsearch"></a><code class="literal">spring-boot-starter-data-elasticsearch</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Elasticsearch search and analytics engine and Spring Data Elasticsearch</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-elasticsearch/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-integration" href="#spring-boot-starter-integration"></a><code class="literal">spring-boot-starter-integration</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Integration</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-integration/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-test" href="#spring-boot-starter-test"></a><code class="literal">spring-boot-starter-test</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for testing Spring Boot applications with libraries including JUnit, Hamcrest and Mockito</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-test/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-hornetq" href="#spring-boot-starter-hornetq"></a><code class="literal">spring-boot-starter-hornetq</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for JMS messaging using HornetQ. Deprecated as of 1.4 in favor of <a class="link" href="#spring-boot-starter-artemis"><code class="literal">spring-boot-starter-artemis</code></a></p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-hornetq/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-jdbc" href="#spring-boot-starter-jdbc"></a><code class="literal">spring-boot-starter-jdbc</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using JDBC with the Tomcat JDBC connection pool</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-jdbc/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-mobile" href="#spring-boot-starter-mobile"></a><code class="literal">spring-boot-starter-mobile</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building web applications using Spring Mobile</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-mobile/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-validation" href="#spring-boot-starter-validation"></a><code class="literal">spring-boot-starter-validation</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Java Bean Validation with Hibernate Validator</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-validation/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-hateoas" href="#spring-boot-starter-hateoas"></a><code class="literal">spring-boot-starter-hateoas</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building hypermedia-based RESTful web application with Spring MVC and Spring HATEOAS</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-hateoas/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-jersey" href="#spring-boot-starter-jersey"></a><code class="literal">spring-boot-starter-jersey</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building RESTful web applications using JAX-RS and Jersey. An alternative to <a class="link" href="#spring-boot-starter-web"><code class="literal">spring-boot-starter-web</code></a></p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-jersey/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-neo4j" href="#spring-boot-starter-data-neo4j"></a><code class="literal">spring-boot-starter-data-neo4j</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Neo4j graph database and Spring Data Neo4j</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-neo4j/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-websocket" href="#spring-boot-starter-websocket"></a><code class="literal">spring-boot-starter-websocket</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building WebSocket applications using Spring Framework’s WebSocket support</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-websocket/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-aop" href="#spring-boot-starter-aop"></a><code class="literal">spring-boot-starter-aop</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for aspect-oriented programming with Spring AOP and AspectJ</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-aop/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-amqp" href="#spring-boot-starter-amqp"></a><code class="literal">spring-boot-starter-amqp</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring AMQP and Rabbit MQ</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-amqp/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-cassandra" href="#spring-boot-starter-data-cassandra"></a><code class="literal">spring-boot-starter-data-cassandra</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Cassandra distributed database and Spring Data Cassandra</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-cassandra/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-social-facebook" href="#spring-boot-starter-social-facebook"></a><code class="literal">spring-boot-starter-social-facebook</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Social Facebook</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-social-facebook/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-jta-atomikos" href="#spring-boot-starter-jta-atomikos"></a><code class="literal">spring-boot-starter-jta-atomikos</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for JTA transactions using Atomikos</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-jta-atomikos/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-security" href="#spring-boot-starter-security"></a><code class="literal">spring-boot-starter-security</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Security</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-security/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-mustache" href="#spring-boot-starter-mustache"></a><code class="literal">spring-boot-starter-mustache</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building MVC web applications using Mustache views</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-mustache/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-jpa" href="#spring-boot-starter-data-jpa"></a><code class="literal">spring-boot-starter-data-jpa</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Data JPA with Hibernate</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-jpa/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter" href="#spring-boot-starter"></a><code class="literal">spring-boot-starter</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Core starter, including auto-configuration support, logging and YAML</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-velocity" href="#spring-boot-starter-velocity"></a><code class="literal">spring-boot-starter-velocity</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building MVC web applications using Velocity views. Deprecated since 1.4</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-velocity/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-groovy-templates" href="#spring-boot-starter-groovy-templates"></a><code class="literal">spring-boot-starter-groovy-templates</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building MVC web applications using Groovy Templates views</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-groovy-templates/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-freemarker" href="#spring-boot-starter-freemarker"></a><code class="literal">spring-boot-starter-freemarker</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for building MVC web applications using FreeMarker views</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-freemarker/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-batch" href="#spring-boot-starter-batch"></a><code class="literal">spring-boot-starter-batch</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Batch</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-batch/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-redis" href="#spring-boot-starter-redis"></a><code class="literal">spring-boot-starter-redis</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Redis key-value data store with Spring Data Redis and the Jedis client. Deprecated as of 1.4 in favor of <a class="link" href="#spring-boot-starter-data-redis"><code class="literal">spring-boot-starter-data-redis</code></a></p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-redis/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-social-linkedin" href="#spring-boot-starter-social-linkedin"></a><code class="literal">spring-boot-starter-social-linkedin</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Stater for using Spring Social LinkedIn</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-social-linkedin/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-cache" href="#spring-boot-starter-cache"></a><code class="literal">spring-boot-starter-cache</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Framework’s caching support</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-cache/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-solr" href="#spring-boot-starter-data-solr"></a><code class="literal">spring-boot-starter-data-solr</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using the Apache Solr search platform with Spring Data Solr</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-solr/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-mongodb" href="#spring-boot-starter-data-mongodb"></a><code class="literal">spring-boot-starter-data-mongodb</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using MongoDB document-oriented database and Spring Data MongoDB</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-mongodb/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-jooq" href="#spring-boot-starter-jooq"></a><code class="literal">spring-boot-starter-jooq</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using jOOQ to access SQL databases. An alternative to <a class="link" href="#spring-boot-starter-data-jpa"><code class="literal">spring-boot-starter-data-jpa</code></a> or <a class="link" href="#spring-boot-starter-jdbc"><code class="literal">spring-boot-starter-jdbc</code></a></p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-jooq/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-jta-narayana" href="#spring-boot-starter-jta-narayana"></a><code class="literal">spring-boot-starter-jta-narayana</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Spring Boot Narayana JTA Starter</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-jta-narayana/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-cloud-connectors" href="#spring-boot-starter-cloud-connectors"></a><code class="literal">spring-boot-starter-cloud-connectors</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Cloud Connectors which simplifies connecting to services in cloud platforms like Cloud Foundry and Heroku</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-cloud-connectors/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-jta-bitronix" href="#spring-boot-starter-jta-bitronix"></a><code class="literal">spring-boot-starter-jta-bitronix</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for JTA transactions using Bitronix</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-jta-bitronix/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-social-twitter" href="#spring-boot-starter-social-twitter"></a><code class="literal">spring-boot-starter-social-twitter</code></p></td><td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left" valign="top"><p>Starter for using Spring Social Twitter</p></td><td style="border-bottom: 0.5pt solid ; " align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-social-twitter/pom.xml" target="_top">Pom</a></p></td></tr><tr><td style="border-right: 0.5pt solid ; " align="left" valign="top"><p><a name="spring-boot-starter-data-rest" href="#spring-boot-starter-data-rest"></a><code class="literal">spring-boot-starter-data-rest</code></p></td><td style="border-right: 0.5pt solid ; " align="left" valign="top"><p>Starter for exposing Spring Data repositories over REST using Spring Data REST</p></td><td style="" align="left" valign="top"><p><a class="link" href="https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-starters/spring-boot-starter-data-rest/pom.xml" target="_top">Pom</a></p></td></tr></tbody>
</table>
</div>
</div>



