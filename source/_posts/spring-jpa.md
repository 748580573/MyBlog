---
title: spring-jpa 文档
date: 2018-06-22 15:50:05
categories: spring
tags: spring
---

# Spring Data JPA 参考文档
<b>注：转发请标明出处</b>

## 前言

<b>Spring Data JPA为Java Persistence  API（JPA）提供存储库支持。 它简化了需要访问JPA数据源的应用程序的开发。</b>

## 项目元数据
<li><b>[Version control](http://github.com/spring-projects/spring-data-jpa)</b></li>
<li><b>[Bugtracker ](https://jira.spring.io/browse/DATAJPA)</b></li>
<li><b>[Release repository ](https://repo.spring.io/libs-release)</b></li>
<li><b>[Milestone repository]( https://repo.spring.io/libs-milestone)</b></li>
<li><b>[Milestone repository ](https://repo.spring.io/libs-milestone)</b></li>
<li><b>[Snapshot repository](https://repo.spring.io/libs-snapshot)</b></li>

## 值得关注的新东西

### Spring Data JPA 1.11中的新特性

<b>Spring Data JPA 1.11增加了以下功能：</b>

<b><li>改进了与Hibernate 5.2的兼容性。</li></b>
<b><li>通过模板为查询支持任意匹配模式</li><b>
<b><li>优化了分页查询</li></b>
<b><li>为存储映射支持查询推导</b></li>

### Spring Data JPA 1.10中的新特性

<b>Spring Data JPA 1.10增加了以下功能：</b>

<li><b>支持存储映射支持查询方法</b></li>
<li><b>支持模板查询</b></li>
<li><b>下面的注释可以构建在组合的注释之上:@EntityGraph、@Lock、@ modify、@Query、@ queryhint和@Procedure。</b></li>
<li><b>支持包含有关集合表达式的关键字。</b></li>
<li><b>AttributeConverter实现了JSR-310的ZoneId和ThreeTenBP</b></li>
<li><b>Upgrade to Querydsl 4, Hibernate 5, OpenJPA 2.4, and EclipseLink 2.6.1.</b></li>

### Dependencies

<li><b>由于各个Spring Data模块的初始日期不同，它们中的大多数都带有不同的主版本号和次版本号。找到兼容版本的最简单方法是依靠Spring Data Release Train BOM，我们将其与定义的兼容版本一起发布，在Maven项目中，您将在POM的<dependencyManagement />部分中声明这种依赖关系，如下所示：</b></li>
<b>ep1：使用Spring Data release train BOM</b>

````xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-releasetrain</artifactId>
      <version>${release-train}</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
  </dependencies>
</dependencyManagement>
````

<b>目前的发行版本是Kay-SR8。 发行版名称按字母顺序上升，目前可用的发行版在此处列出。 版本名称遵循以下模式：$ {name} - $ {release}，其中release可以是下列之一：</b>

<li><b>BUILD-SNAPSHOT：快照</b></li>
<li><b>M1, M2等: 候选版</b></li>
<li><b>RELEASE：正式发布版</b></li>
<li><b>SR1, SR2等：服务版</b></li>

<b>在我们的[Spring Data示例存储库](https://github.com/spring-projects/spring-data-examples/tree/master/bom)中可以找到使用BOM的一个工作示例。 有了这个，你可以在<dependencies/ >块中声明你想在没有版本的情况下使用Spring Data模块，如下所示</b>

<b>ep2：声明Spring Data模块的依赖关系</b>

````xml
<dependencies>
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
  </dependency>
<dependencies>
````

### Spring Boot的依赖管理

<b>Spring Boot为您选择最新版本的Spring Data模块。 如果您仍想升级到更新版本，请将属性spring-data-releasetrain.version配置为您要使用的版本名称和版本号。</b>

###  Spring Framework

<b>当前的Spring Data模块版本需要Spring Framework 5.0.7版本或更高版本。这些版本可以与旧版本以及次版本兼容，但是，强烈建议使用该代中的最新版本。

## 使用Spring Data Repositories

<b>Spring Data repository抽象化的目的是减少各种持久性存储实现数据访问层所需的样板代码数量。</b>

````
本章介绍Spring Data存储库的核心概念和接口。 本章中的信息来自Spring Data Commons模块。它使用Java Persistence API（JPA）模块的配置和代码示例。 您应该将XML
命名空间声明和要扩展的类型调整为您使用的特定模块的等同项。  “Namespace reference”涵盖了所有支持repository API的Spring Data 模块的xml配置文件。
````

### 核心概念

<b>Spring Data核心的接口是Repository,它需要将类和类的主键作为类型参数， 该接口主要作为标记接口来捕获要使用的类型，并帮助你扩展这接口。CrudRepository为需要管理的实体类提供复杂的CRUD功能。</b>

````java
public interface CrudRepository<T, ID extends Serializable>
  extends Repository<T, ID> {

  <S extends T> S save(S entity);        //  (1)

  Optional<T> findById(ID primaryKey);   //  (2)

  Iterable<T> findAll();                //  (3)

  long count();                         //  (4) 

  void delete(T entity);               //  (5)

  boolean existsById(ID primaryKey);   //  (6)

  // … more functionality omitted.
}
````
<b>(1)保存entity</b>
<b>(2)根据ID返回entity</b>
<b>(3)返回所有的entity</b>
<b>(4)返回entity的总数</b>
<b>(5)删除指定的entity</b>
<b>(6)查询指定ID的entity是否存在</b>

````
     我们提供了特定的特定的持久性抽象技术，如JpaRepository或MongoRepository。这些接口扩展了CrudRepository并且暴露了深层次的持久性技术，以及通用未知的持久性技术接口，
例如，CrudRepository。

````

<b>在CrudRepository之上，有一个PagingAndSortingRepository抽象，它增加了额外的方法来简化对实体的分页访问：</b>

<b>pg4：PagingAndSortingRepository 接口

````java
public interface PagingAndSortingRepository<T, ID extends Serializable>
  extends CrudRepository<T, ID> {

  Iterable<T> findAll(Sort sort);

  Page<T> findAll(Pageable pageable);
  
}
````

<b>要访问分页大小为20，User数据的第二页，你可以执行一下操作：</b>

````java
public class Test{
    public static void main(String[] args){
      PagingAndSortingRepository<User, Long> repository ;// … get access to a bean
      Page<User> users = repository.findAll(new PageRequest(1, 20));
    }
}
````
<b>除了查询方法外，count()与delete()也都扩展至查询方法，下面列出了怎么定义count接口。</b>

<b>ep5：扩展count</b>

````java
interface UserRepository extends CrudRepository<User, Long> {

  long countByLastname(String lastname);
}
````

<b>下面列出了怎么定义delete接口。</b>

<b>ep6：扩展delete</b>

````java
interface UserRepository extends CrudRepository<User, Long> {

  long deleteByLastname(String lastname);

  List<User> removeByLastname(String lastname);
}
````

### query 方法

<b>标准CRUD功能存储库通常会在底层数据存储上进行查询。 使用Spring Data声明这些查询需要四个过程：</b>

<b>1 声名一个Repository扩展接口或者其子类，它需要键入需要处理的domain class 和 ID,实例如下：</b>
````java
interface PersonRepository extends Repository<Person, Long> { 
    // … 
}
````
<b>2 通过在接口中声名查询方法</b>

````java
interface PersonRepository extends Repository<Person, Long> {
  List<Person> findByLastname(String lastname);
}
````
<b>3 通过Spring的JavaConfig 或者 XML创建代理实例

<b>a 用JavaConfig创建class，像这样：

````java
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@EnableJpaRepositories
class Config {}
````

<b>b 用XML创建class，像这样：

````xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:jpa="http://www.springframework.org/schema/data/jpa"
   xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/data/jpa
     http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

   <jpa:repositories base-package="com.acme.repositories"/>

</beans>
````
<b>这个例子中使用了JPA命名空间。如果你想为其他的数据库使用这个repository抽象，你需要为你使用的数据库modle改变相应的命名空间，换句话说，你需要改变jap支持，例如mongodb。
<b>另外，请注意，JavaConfig没有显式地配置包，因为默认使用的是注释类的包。要自定义要扫描的包，请使用`basePackage…`指定的数据存储库的`@Enable${store}Repositories`注释的一个属性,例如`@EnableJpaRepositories`

<b>注入存储库实例并使用它，如下面的示例所示</b>
````java
class SomeClient {

  private final PersonRepository repository;

  SomeClient(PersonRepository repository) {
    this.repository = repository;
  }

  void doSomething() {
    List<Person> persons = repository.findByLastname("Matthews");
  }
}
````

<b>这部分详细解释每一步：</b>
<li>定义Resposiroty接口</li>
<li>定义Query方法</li>
<li>创建Respository</li>
<li>Spring Data Repositories自定义实现</li>

### 定义Resposiroty接口

<b>首先定义一个特定的domain类接口，这个接口必须扩展`Respository`并且键入domain类和ID(主键),如果你想暴露出该domain类的方发，则要扩展`CrudRepository `接口而不是`Repository`接口。

#### 调整Respository定义

<b>通常，您的存储库接口扩展了`Repository`，`CrudRepository`或`PagingAndSortingRepository`。 或者，如果您不想扩展Spring Data接口，也可以使用`@RepositoryDefinition`注释您的存储库接口。 扩展`CrudRepository`公开了一套完整的方法来操纵您的实体。如果您想有选择性的暴露一些方法，请从`CrudRepository `复制您想暴露的方法到你的domain Resposiroty。

````
这样做可以让您在提供的Spring Data Repositories功能之上定义自己的抽象

````

<b>下面的例子演示如何有选择性的暴露CRUD方法 (findById and save):

<b>ep7：选择性的暴露CRUD方法

````java
@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends Repository<T, ID> {

  Optional<T> findById(ID id);

  <S extends T> S save(S entity);
}

interface UserRepository extends MyBaseRepository<User, Long> {
  User findByEmailAddress(EmailAddress emailAddress);
}
````

<b>在前面的实例中，你为所有的domain resposiroty定义了一个公共的基本接口，并暴露出findById()和save()方法，这些方法可以追加到到您选择的基本的Spring Data的Resposiroty(例如，如果您使用JPA，实现是SimpleJpaRepository)的实现中去，所以，UserRepository现在可以保存用户，按ID查找单个用户，并通过电子邮件地址查询用户。</b>

````
中间的repository接口（MyBaseRepository）用@NoRepositoryBean进行了注释。Spring DATA在运行时不会为这些接口（MyBaseRepository）创建实例。
````
### 仓库方法的空处理
<b>从Spring Data 2.0开始，Resporitory Crud方法使用java 8的`Optional`返回单个的聚合实例,以表示潜在的缺失值。此外，Spring Data支持在查询方法上返回以下包装器类型:
<b><li>`com.google.common.base.Optional`</li></b>
<b><li>`scala.Option`</li></b>
<b><li>`io.vavr.control.Option`</li></b>
<b><li>`javaslang.control.Option (deprecated as Javaslang is deprecated)`</li></b>


<b>另外，查询方法可以选择不使用包装类型，返回null表示没有查询结果，Repository方法返回collections，collection子类，wrappers和streams，它不会返回空，而是返回相应的null表示，有关详细信息，请参阅“[Repository Query返回类型](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repository-query-return-types)”

#### Nullability注释
<b>您可以使用Spring Framework的Nullability注释来表示存储库方法的可空约束。 它们提供了一个友好的工具方法，并在运行时选择性地进行空检查，如下所示：
<b><li>[@NonNullApi](https://docs.spring.io/spring/docs/5.0.7.RELEASE/javadoc-api/org/springframework/lang/NonNullApi.html)：一个普通的Spring注释，它声明参数和返回值在给定的包中默认为非空值。</li></b>
<b><li>[@NonNull](https://docs.spring.io/spring/docs/5.0.7.RELEASE/javadoc-api/org/springframework/lang/NonNull.html)：一个常见的Spring注释声明注释的元素不能为空。</li></b>
<b><li>[@Nullable](https://docs.spring.io/spring/docs/5.0.7.RELEASE/javadoc-api/org/springframework/lang/Nullable.html)：一个常见的Spring注释声明注释元素在某些情况下可以为null。</li></b>

<b>JSR 305元注释允许工具供应商(如IDEA、Eclipse和Kotlin)以通用的方式提供null安全支持，而无需对Spring注释进行硬编码支持，要启用查询方法的可空性约束的运行时检查，您需要通过在package-info.java中使用Spring的@NonNullApi来激活包级别上的不可空性，如下例所示:

````java
@org.springframework.lang.NonNullApi
package com.acme;
````
<b>一旦存在非空默认值，repository query方法将在运行时验证可空性约束。如果查询执行结果违反了定义的约束，则会引发异常。默认情况下该注释定义在repository的包上。</b>

<b>下面的例子展示了一些刚刚描述的技术。</b>

<b>ep9：使用不同的nullability约束</b>

````java
package com.acme;                                                          //(1)    

import org.springframework.lang.Nullable;

interface UserRepository extends Repository<User, Long> {

  User getByEmailAddress(EmailAddress emailAddress);                      //(2)    

  @Nullable
  User findByEmailAddress(@Nullable EmailAddress emailAdress);           //(3)   

  Optional<User> findOptionalByEmailAddress(EmailAddress emailAddress); //(4) 
}
````

<b>(1)respository驻留在我们定义具有非空行为的包（或者子包下面） (我的理解是，这个注解是在com.acme或它的子包中使用了,嗯...emailAdress类是在这些包下定义的，如果我的理解有错，还请读者指出来)</b>
<b>(2)当执行的查询不产生结果时抛出EmptyResultDataAccessException，当传入的emailAddress为null时抛出IllegalArgumentException。</b>
<b>(3)当执行的查询不产生结果时返回null。 也可以为emailAddress传null值</b>
<b>(4)当查询没有结果时换回Optional.empty()，当emailAddress传入参数为空时抛出IllegalArgumentException</b>

### 与多个Spring data module一起使用Respository

<b>在应用程序中使用唯一的Spring数据模块使事情变得简单，因为在定义的范围中所有的Respository接口都绑定到Spring数据模块，有时，应用程序需要使用多个Spring Data模块。 在这种情况下，存储库定义必须区分持久性技术。当它在类路径中检测到多个Respository工厂时，Spring Data会进入严格的Resporitory配置模式。为Spring Data moudle绑定一个Respository需要为Respository或者domain 类使用严格的配置。</b>

<ol>
<b><li type> 如果Respository扩展了一个特定的Respository，Spring data就可以集成并使用它。</li></b>
<b><li type> 如果domain类被指定类型的注解注解，则Spring DATA就可以集成使用它，Spring Data模块接受第三方注释（比如JPA的@Entity）或者提供他们自己的注释（比如@Document for Spring Data MongoDB和Spring Data Elasticsearch）。</li></b>
</ol>

<b>以下示例显示使用特定的模块接口（本例中为JPA）的Respository：</b>
<b>ep11：用特定的模块接口定义Respository
````java
interface MyRepository extends JpaRepository<User, Long> { }

@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends JpaRepository<T, ID> {
  //…
}

interface UserRepository extends MyBaseRepository<User, Long> {
  //…
}
````
<b>Repository和UserRepository将JpaRepository扩展到它们的类型层次结构中。 它们可以在Spring Data JPA模块中被使用。</b>

<b>以下示例演示使用通用的Repository接口</b>
<b>ep：12 使用通用接口定义Repository</b>

````java
interface AmbiguousRepository extends Repository<User, Long> {
 //…
}

@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends CrudRepository<T, ID> {
  //…
}

interface AmbiguousUserRepository extends MyBaseRepository<User, Long> {
  //…
}
````
<b>`AmbiguousRepository`和`AmbiguousUserRepository`仅将`Repository`和`CrudRepository`扩展到它们的类型层次结构中。</b>

<b>用注解定义Resporitory </b>

<b>ep13：</b>
````java
interface PersonRepository extends Repository<Person, Long> {
 //…
}

@Entity
class Person {
  //…
}

interface UserRepository extends Repository<User, Long> {
 //…
}

@Document
class User {
  //…
}
````
<b>PersonRepository引用Person，它使用JPA @Entity注释进行注释，因此该Respository属于Spring Data JPA。UserRepository引用User，它使用Spring Data MongoDB的@Document注释进行注释。</b>

<b>下面是一个不好的实例，该实例使用了混合的注释。</b>

<b>ep：14</b>

````java
interface JpaPersonRepository extends Repository<Person, Long> {
 //…
}

interface MongoDBPersonRepository extends Repository<Person, Long> {
 //…
}

@Entity
@Document
class Person {
  //…
}
````

<b>此示例使用JPA和Spring Data MongoDB注释了domain class。这样它就定义了两个Respository，JpaPersonRepository和MongoDBPersonRepository。一个用于JPA，另一个用于MongoDB的使用，这样就会导致未定义的行为，以致Spring Data就不能区分出两个Respository。</b>

<b>需要在domain class上严格的使用Respository注释，这样Spring Data才能识别它是哪个Respository。可以在domain类上使用多个关于持久性的技术，也可以在domain类上使用不同的Respository注解，但这样的话Spring Data就不能确定使用的是哪个Respository，这样是会出问题的。</b>
<b>区分Respository的最后一种方法是通过确定Respository基础包的范围。 基本软件包定义了扫描Respository接口的起点，这就暗示了repository是在哪个基础包下了。

<b>下面的理解演示用注解扫描基础包</b>
<b>ep15：</b>

````java
@EnableJpaRepositories(basePackages = "com.acme.repositories.jpa")
@EnableMongoRepositories(basePackages = "com.acme.repositories.mongo")
interface Configuration { }
````

### 查询方法定义

<b>repository 有两种代理方法去驱动指定存储查询方法。</b>
<b><li>直接通过查询的方法名来驱动</li></b>
<b><li>用自己写的方法</li></b>

<b>有实际情况决定使用哪种方法，但是必须要有一种策略来判断创建什么类型的查询,等等我们会慢慢简绍有哪些可用的选项</b>

#### 查询策略 

<b>以下是解决respository查询方法结构的策略， 通过XML配置，您可以通过query-lookup-strategy属性在命名空间配置策略。 对于Java配置，您可以使用Enable $ {store}存储库注释的queryLookupStrategy属性。 某些策略可能不支持特定的数据存储。</b>
<b><li>`create`： 试图通过查询方法名称来创建查询。 通常的做法是删除指定的方法前缀然后解析剩下的方法名。指定方法前缀的文档“[Query Creation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-creation)”.</li></b>
<b><li>`USE_DECLARED_QUERY`：试图找到一个已声明的查询,如果找不到就抛出一个异常。查询可以通过某处的注释或其他方式声明。</li></b>
<b><li>`CREATE_IF_NOT_FOUND `：结合了`CREATE`和`USE_DECLARED_QUERY`。 它首先查找已声明的查询，并且如果未找到已声明的查询，则会创建一个自定义的基于方法名称的查询。这是默认的查找策略，因此，如果您没有明确配置任何内容，就会使用它。 它允许通过方法名称进行快速查询定义，还可以根据需要引入已声明的查询来自定义这些查询。</li></b>

#### 创建查询方法
<b>Spring Data内置查询机制，他将带有 find…By, read…By, query…By, count…By, and get…By前缀从方法中剥离出来，然后解析其余部分。子句可以包含表达式，第一个by作为分隔符表示实际查询条件的开始。在一些简单查询中，你可以添加一些条件并将他们与and或or连接起来，下面的示例展示了如何创建多个查询:</b>

<b>例 13. 根据方法名创建查询</b>
````java
interface PersonRepository extends Repository<User, Long> {

  List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

  // Enables the distinct flag for the query
  List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
  List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

  // Enabling ignoring case for an individual property
  List<Person> findByLastnameIgnoreCase(String lastname);
  // Enabling ignoring case for all suitable properties
  List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

  // Enabling static ORDER BY for a query
  List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
  List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
}
````
<b>解析方法的实际结果取决于您创建查询的数据库，有一些东西需要注意：</b>
<b><li>遍历表达式通常结合运算符连接。您可以把表达式 And 和 Or , Between , LessThan (不超过) , GreaterThan , Like 等运算符结合来用，这些操作对不同的数据库可能有所不同。</li></b>
<b><li>方法解析器支持为单个属性设置一个IgnoreCase标记(例如，findByLastnameIgnoreCase(…))或者忽略所有属性的大小写(通常是字符串实例——例如`findByLastnameAndFirstnameAllIgnoreCase(…)`),实际效果因使用的数据库不用而不同</li></b>
<b><li>你可以添加OrderBy为查询方法提供排序（Asc或Desc）“[Special parameter handling](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters)”.

<b>更多干货，[点这里](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repository-query-keywords)</b>






