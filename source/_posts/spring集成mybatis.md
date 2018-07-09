---
title: spring集成mybatis
date: 2018-07-09 16:55:18
categories: springs
tags: spring
---

# spring集成mybatis

## 集成集成iBATIS

<b>Spring通过使用如下Bean进行集成iBATIS：</b>

<b><li>SqlMapClientFactoryBean：用于集成iBATIS。</li></b>

<b>configLocation和configLocations：用于指定SQL Map XML配置文件，用于指定如数据源等配置信息；</b>

<b>mappingLocations：用于指定SQL Map映射文件，即半自动概念中的SQL语句定义；</b>

<b>sqlMapClientProperties：定义iBATIS 配置文件配置信息；</b>

<b>dataSource：定义数据源。</b>

````xml
    <bean id="****" class="org.springframework.orm.ibatis.SqlMapClientFactoryBean">
        <property name="dataSource" ref="****"/>
        <property name="configLocation" value="****"/>
    </bean>
````

##  iBATIS配置文件

````xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMapConfig PUBLIC "-//ibatis.apache.org//DTD SQL Map Config 2.0//EN"
    "http://ibatis.apache.org/dtd/sql-map-config-2.dtd">
 
<sqlMapConfig>
    <settings enhancementEnabled="true" useStatementNamespaces="true"
        maxTransactions="20" maxRequests="32" maxSessions="10"/>
        
    <typeAlias alias="DataSourceType" type="******"/>
    
    <typeHandler javaType="DataSourceType" callback="**"/>

    <sqlMap resource="/***/**"/>
</sqlMapConfig>
````

<b>这里讲一下<typeHandler>标签的作用，在我们利用mybatis作为持久层框架存储数据时，从mybatis接收参数到mysql存储数据，都会用到typeHandler类型处理器。这也就是从JavaType->JdbcType的转化过程。由于mybatis初始时已经内置大部分基础类型转化的TypeHandler，已经足够我们平常的简单应用开发了，所以大多数情况下并不需要我们自己去定义类型转换器。但是，当遇到一些特殊情况时，为了开发的方便性，我们才回去自定义一些类型转换器。<b>
<b>使用场景：mybatis在预处理语句（PreparedStatement）中设置一个参数时，或者从结果集（ResultSet）中取出一个值时，都会用到TypeHandler。它的作用就是将java类型（javaType）转化为jdbc类型（jdbcType），或者将jdbc类型（jdbcType）转化为java类型（javaType）。</b>
<b>对TypeHandler接口的一些说明：<b>

<b>TypeHandler是一个接口，它定义了如下四个方法，实现类必须去实现，方法如下：</b>

````java
void setParameter(PreparedStatement var1, int var2, T var3,     JdbcType var4) throws SQLException;

    T getResult(ResultSet var1, String var2) throws SQLException;

    T getResult(ResultSet var1, int var2) throws SQLException;

    T getResult(CallableStatement var1, int var2) throws SQLException;
}
````

<b>setParameter：通过preparedStatement对象设置参数，将T类型的数据存入数据库。</b>

<b>getResult：通过列名或者下标来获取结果数据，也可以通过CallableStatement获取数据。</b>

<b>自定义注解里还可以通过注解配置java类型和jdbc类型： </b>
<b>@MappedTypes() :注解配置java类型 </b>
<b>@MappedJdbcTypes()： 注解配置jdbc类型</b>

<b>枚举类型的TypeHandler： </b>
<b>EnumOrdinalTypeHandler：使用枚举字符串名称作为参数传递。 </b>
<b>EnumTypeHandler：使用整数下标作为参数传递。</b>

<b>为了实现自定义枚举类型的TypeHandler，我们通过性别（Sex）类型来进行演示：</b>
   
<b>首先定义Sex类型的枚举，定义如下：</b>

````java
public enum Sex {
//每一个类型都是一个枚举类（Sex）的实例
    MALE(0, "男"),
    FMALE(1, "女");

    //用于保存在数据库
    private int SexCode;
    //用于UI展示
    private String SexName;

    Sex(int sexCode, String sexName) {
        SexCode = sexCode;
        SexName = sexName;
    }

    public int getSexCode() {
        return SexCode;
    }

//通过SexCode的值来获取Sex枚举类型，数据库只需保存code，通过代码解析成Sex类型
    public static Sex getSexFromCode(int code) {
        for (Sex sex : Sex.values()) {
            if (sex.getSexCode() == code) {
                return sex;
            }
        }
        return null;
    }
}
````

<b>如果我们不自定义TypeHandler，也可以通过mybatis内置的EnumOrdinalTypeHandler来进行类型转换，在mapper文件中的resultmap中进行如下配置：</b>

````xml
<result property="sex" column="sex" typeHandler="org.apache.ibatis.type.EnumOrdinalTypeHandler"/>
````

<b>自定义枚举TypeHandler：</b>

````java
public class SexTypeHandler implements TypeHandler<Sex> {
    @Override
    public void setParameter(PreparedStatement preparedStatement, int i, Sex sex, JdbcType jdbcType) throws SQLException {
        //设置第i个参数的值为传入sex的code值，preparedStatement为执行数据库操纵的对象
        //传值的时候是一个sex对象，但是当进行映射插入的时候就会转化为sex的code值进行存储
        preparedStatement.setInt(i, sex.getSexCode());
    }

    @Override
    public Sex getResult(ResultSet resultSet, String s) throws SQLException {
        //获取数据库存储的sex的code值
        int result = resultSet.getInt(s);
        return Sex.getSexFromCode(result);
    }

    @Override
    public Sex getResult(ResultSet resultSet, int i) throws SQLException {
        int result = resultSet.getInt(i);
        return Sex.getSexFromCode(result);
    }

    @Override
    public Sex getResult(CallableStatement callableStatement, int i) throws SQLException {
        int result = callableStatement.getInt(i);
        return Sex.getSexFromCode(result);
    }
}
````

<b>最后需要在配置文件中运用我们自定义的TypeHandler对Sex进行类型转换：</b>

````xml
   <resultMap id="userMap" type="com.cbg.pojo.UserBean">
        <result property="id" column="id"/>
        <result property="userName" column="user_name"/>
        <result property="birthday" column="birthday"/>
        <result property="mobile" column="mobile"/>
        <result property="email" column="email"/>
        <result property="note" column="note"/>
        <result property="sex" column="sex" javaType="com.cbg.Entity.Sex" jdbcType="INTEGER"
                typeHandler="com.cbg.handler.SexTypeHandler"/>
    </resultMap>
````

<b>通过上面的配置，自定义的TypeHandler就会生效，对java中的Sex对象和数据库中的Integer进行自定转换。数据库只需存储Sex的code值，获取数据库数据时自动转化为Sex枚举对象，使用相当方便，也更加灵活。</b>
