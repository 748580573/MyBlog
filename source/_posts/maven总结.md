---
title: maven总结
date: 2018-12-14 11:40:36
categories: maven
tags: maven
---

# 命令

````
maven在执行一个生命周期的命令的是时候将会执行之前的所有生命周期操作，比如执行mvn install，会执行前面一系列的动作包括 compile , package , test 等
````

**mvn clean**
````
执行mvn clean可将根目录下生成的target文件移除
````

**mvn package**
````
对项目进行打包，例如打包位jar文件或war文件,and so on......
````

**maven build**
````
将你打好的jar包安装到你的本地库中，一般没有设置过是在 用户目录下的 .m2\下面。
````

**mvn install -Dmaven.test.skip**

````
打包时，跳过测试阶段
````

**mvn dependency:sources**
````
取所有在POM中的的source code
````

**mvn dependency:resolve -Dclassifier=javadoc**
````
取所有在POM中的的source Javadocs
````

# pom文件



