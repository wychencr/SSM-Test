---
title: 使用IDEA整合SSM框架
date: 2018-11-01 16:35:29
tags: [Java,Spring,SpringMVC,MyBatis]
categories: java
---



## 一、安装环境和开发工具

在整合**Spring，SpringMVC** 和 **MyBatis** 的过程中，很容易遇到一些小问题，因此记录下整合过程。

首先是安装环境和开发工具，如下：

- Window 7
- Jdk 1.8
- MySql 8.0
- Maven 3.5.4
- Tomcat 8.5.34
- IntelliJ IDEA 2018



## 二、整体项目结构

整个项目在Maven WebApp模板工程的基础上构建，不同类型的文件放置于不同的包或者路径下，全部配置完成后的工程结构如下图所示：



![](http://ww1.sinaimg.cn/large/e4eff812gy1fwso417uj6j20ax0kpglm.jpg)



不同路径下的文件归类说明如下表(classpath相当于resources文件夹)：

| 包名/路径名    | 作用                      |
| -------------- | ------------------------- |
| controller     | 存放控制器                |
| mapper         | DAO层接口                 |
| pojo           | 实体类                    |
| service        | 业务类（接口+实现）       |
|                |                           |
| /com/cr/mapper | 对应mapper下接口的xml文件 |
| /spring        | 与spring相关的配置文件    |
| /webapp/jsp    | 存放jsp文件               |
| /*.properties  | 资源文件                  |



## 三、配置步骤

### 1、添加Maven依赖

- 新建一个Maven WebApp工程，在**pom.xml**添加依赖，主要包括spring、springmvc、 mybatis、mybatis-spring  、servlet 、mysql 等项的jar包。

> **pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.cr</groupId>
  <artifactId>SSMTest</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>SSMTest Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.8.RELEASE</spring.version>
    <jackson.version>2.9.3</jackson.version>
  </properties>

  <dependencies>
    <!--添加Spring MVC的依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--添加Servlet的依赖-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
    <!--JSTL用于在控制器中将模型绑定到JSP中-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
    <!--添加spring的依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context-support</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <!--利用它处理事务问题-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <!--添加数据库Mysql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <!--驱动版本号一定要装正确 否则连不上 mysql 5.1.46 可以适用本机-->
      <version>5.1.46</version>
    </dependency>

    <!--添加Mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.2.6</version>
    </dependency>

    <!--整合Mybatis与Spring的依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>
    <!-- Mybatis分页依赖 -->
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>5.1.2</version>
    </dependency>
    <!-- 处理时间日期格式 -->
    <dependency>
      <groupId>joda-time</groupId>
      <artifactId>joda-time</artifactId>
      <version>2.9.9</version>
    </dependency>
    <!-- 用于MD5加密 -->
    <dependency>
      <groupId>commons-codec</groupId>
      <artifactId>commons-codec</artifactId>
      <version>1.10</version>
    </dependency>

    <!--日志-->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.12</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.12</version>
    </dependency>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>


    <!--添加Json依赖-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>${jackson.version}</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>${jackson.version}</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>${jackson.version}</version>
    </dependency>
    <!--有时候不加可能会报错-->
    <dependency>
      <groupId>net.sf.json-lib</groupId>
      <artifactId>json-lib</artifactId>
      <version>2.4</version>
      <classifier>jdk15</classifier>
    </dependency>

    <!-- 新添加处理json为java bean -->
    <dependency>
      <groupId>org.codehaus.jackson</groupId>
      <artifactId>jackson-core-asl</artifactId>
      <version>1.9.13</version>
    </dependency>
    <dependency>
      <groupId>org.codehaus.jackson</groupId>
      <artifactId>jackson-mapper-asl</artifactId>
      <version>1.9.13</version>
    </dependency>
    <!-- 文件上传 高版本可以使用Multipart解析器 就不用引入这个包了 -->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.1</version>
    </dependency>
    <!-- 数据源的引入， 池化技术 -->
    <dependency>
      <groupId>com.mchange</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.2.1</version>
    </dependency>
    <dependency>
      <groupId>commons-dbcp</groupId>
      <artifactId>commons-dbcp</artifactId>
      <version>1.2.2</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>SSMTest</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.0.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.7.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.20.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

添加完成后的maven依赖如下：

![](http://ww1.sinaimg.cn/large/e4eff812gy1fwsrj05xh2j20bv0gfgll.jpg)



### 2、配置web.xml

- spring上下文文件路径配置

  ```xml
  <!--配置Spring IoC的配置文件路径 classpath相当于resources文件夹-->
  <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring/applicationContext.xml</param-value>
  </context-param>
  ```

  这里指定了上下文配置文件为spring文件夹下的**applicationContext.xml**，稍后再配置这个文件。

  

- Log4j配置

  ```
  <context-param>
      <param-name>log4jRefreshInterval</param-name>
      <param-value>60000</param-value>
  </context-param>
  ```



- ContextLoaderListener监听器配置

  ```xml
  <listener>
      <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <!-- 防止Spring内存溢出监听器 -->
  <listener>
      <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
  </listener>
  ```



- DispatcherServlet前置控制器配置

  ```xml
  <servlet>
      <!--springmvc框架默认自动找到/WEB-INF/springmvc-servlet.xml作为配置文件载入web工程中 这里手动设置位置-->
      <servlet-name>springmvc</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:spring/springmvc-servlet.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
      <async-supported>true</async-supported>
  </servlet>
  ```

  springmvc框架默认自动找到/WEB-INF/[servlet-name]-servlet.xml作为配置文件载入web工程中 这里手动设置位置为spring文件夹下的**springmvc-servlet.xml**，稍后再配置这个文件。

  

- Servlet拦截设置

  ```xml
  <servlet-mapping>
      <servlet-name>springmvc</servlet-name>
      <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```



> **web.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!-- Spring配置 -->
    <!-- ================================================= -->
    <!--配置Spring IoC的配置文件路径 classpath相当于resources文件夹-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/applicationContext.xml</param-value>
    </context-param>
    <!--Log4j配置-->
    <context-param>
        <param-name>log4jRefreshInterval</param-name>
        <param-value>60000</param-value>
    </context-param>
    <!--配置ContextLoaderListener初始化IOC容器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!-- 防止Spring内存溢出监听器 -->
    <listener>
        <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
    </listener>

    <!-- Spring mvc配置 -->
    <!-- ================================================= -->
    <!--DispatcherServlet前置控制器配置-->
    <servlet>
        <!--springmvc框架默认自动找到/WEB-INF/springmvc-servlet.xml作为配置文件载入web工程中 这里手动设置位置-->
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/springmvc-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
        <async-supported>true</async-supported>
    </servlet>
    <!--拦截内容：servlet映射设置-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```



### 3、applicationContext.xml配置



- 在spring文件夹下建立**applicationContext.xml**

  

> **applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--引入spring和其他整合的配置文件 比如spring-mybatis.xml等-->
    <import resource="classpath:spring/spring-*.xml"/>

</beans>
```

- 这里引入spring和其他整合的配置文件 比如**spring-mybatis.xml**等，接下来在spring文件夹下新建一个spring-mybatis.xml文件



### 4、spring-mybatis.xml配置

- 配置自动扫描，需要扫描到**控制层**和**服务层**，刚开始我这里写成了com.cr.mapper，结果导致控制器不能注入

  ```xml
  <context:component-scan base-package="com.cr" />
  ```



- 新建数据库资源文件jdbc.properties，针对不同的数据库需要修改配置 同时要注意匹配数据库的版本号，比如我安装的是MySQL 8.0，之前由于驱动版本弄错了，所以总是连接数据库失败，后来改成5.1.46才解决了问题

  ```properties
  # 针对不同的数据库需要修改配置 同时要注意匹配数据库的版本号
  mysql.driver=com.mysql.jdbc.Driver
  mysql.url=jdbc:mysql://localhost:3306/mybatis
  mysql.username=root
  mysql.password=XXXX
  
  #定义初始连接数
  dbcp.initialSize=0
  #定义最大连接数
  dbcp.maxActive=20
  #定义最大空闲
  dbcp.maxIdle=20
  #定义最小空闲
  dbcp.minIdle=1
  #定义最长等待时间
  dbcp.maxWait=60000
  ```



- 引入数据库资源文件

  ```xml
  <bean id="propertyConfigurer"
        class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
      <property name="location" value="classpath:jdbc.properties" />
  </bean>
  ```



- 配置数据库MySQL

  ```xml
  <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
      <property name="driverClassName" value="${mysql.driver}" />
      <property name="url" value="${mysql.url}" />
      <property name="username" value="${mysql.username}" />
      <property name="password" value="${mysql.password}" />
  
  
      <property name="initialSize" value="${dbcp.initialSize}" />
      <property name="maxActive" value="${dbcp.maxActive}" />
      <property name="maxIdle" value="${dbcp.maxIdle}" />
      <property name="minIdle" value="${dbcp.minIdle}" />
      <property name="maxWait" value="${dbcp.maxWait}" />
  </bean>
  ```



- 整合Spring和MyBatis，注意路径的书写方式是"/"不是"."

  ```xml
  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource" />
      <!-- 自动扫描mapping.xml文件 注意路径是"/"不是"."-->
      <property name="mapperLocations" value="classpath:com/cr/mapper/*.xml" />
      <property name="configuration">
          <!--可以将之前mybatis.cfg.xml的一些配置项转移到这里来-->
          <bean class="org.apache.ibatis.session.Configuration">
              <property name="mapUnderscoreToCamelCase" value="true" />
          </bean>
      </property>
  </bean>
  ```



- 扫描持久层接口

  ```xml
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="basePackage" value="com.cr.mapper" />
      <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
  </bean>
  ```

- 数据库事务管理

  ```xml
  <bean id="transactionManager"
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource" />
  </bean>
  ```



> **spring-mybatis.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:tx="http://www.springframework.org/schema/cache"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                        http://www.springframework.org/schema/beans/spring-beans.xsd
                        http://www.springframework.org/schema/aop
                        http://www.springframework.org/schema/aop/spring-aop.xsd
                        http://www.springframework.org/schema/tx
                        http://www.springframework.org/schema/tx/spring-tx.xsd
                        http://www.springframework.org/schema/mvc
                        http://www.springframework.org/schema/mvc/spring-mvc.xsd
                        http://www.springframework.org/schema/cache
                        http://www.springframework.org/schema/cache/spring-cache.xsd
                        http://www.springframework.org/schema/context
                        http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 自动扫描包 包括了控制层和服务层 -->
    <context:component-scan base-package="com.cr" />
    <!-- 引入数据库配置文件 -->
    <bean id="propertyConfigurer"
          class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="classpath:jdbc.properties" />
    </bean>

    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="${mysql.driver}" />
        <property name="url" value="${mysql.url}" />
        <property name="username" value="${mysql.username}" />
        <property name="password" value="${mysql.password}" />


        <property name="initialSize" value="${dbcp.initialSize}" />
        <property name="maxActive" value="${dbcp.maxActive}" />
        <property name="maxIdle" value="${dbcp.maxIdle}" />
        <property name="minIdle" value="${dbcp.minIdle}" />
        <property name="maxWait" value="${dbcp.maxWait}" />
    </bean>

    <!-- 整合Spring和MyBatis，就不需要之前的mybatis配置文件了 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- 自动扫描mapping.xml文件 注意路径是"/"不是"."-->
        <property name="mapperLocations" value="classpath:com/cr/mapper/*.xml" />
        <property name="configuration">
            <!--可以将之前mybatis.cfg.xml的一些配置项转移到这里来-->
            <bean class="org.apache.ibatis.session.Configuration">
                <property name="mapUnderscoreToCamelCase" value="true" />
            </bean>
        </property>
    </bean>

    <!-- 扫描DAO持久层接口 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.cr.mapper" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>

    <!-- 数据库事务管理 -->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
</beans>
```



### 5、springmvc-servlet.xml配置

> **springmvc-servlet.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.1.xsd">

    <!-- scan the package and the sub package -->
    <context:component-scan base-package="com.cr.controller"/>
    <!-- don't handle the static resource -->
    <mvc:default-servlet-handler />

    <!-- if you use annotation you must configure following setting -->
    <mvc:annotation-driven />

    <!-- configure the InternalResourceViewResolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!--相当于寻找/jsp/xxx.jsp文件-->
        <!-- 前缀 -->
        <property name="prefix" value="/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```



### 6、建立实体类

- 在pojo包下新建一个实体User类，包含用户名username 和 密码password 字段，`alt` + `insert` 生成 setter和getter方法



> **User.java**

```java
package com.cr.pojo;


public class User {
    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```



### 7、DAO层建立

- 创建测试用表t_user，并添加两条数据

  ![](http://ww1.sinaimg.cn/large/e4eff812gy1fwssxgcy24j20n402cweb.jpg)

- 在mapper包下新建一个**UserMapper**接口，包含一个select查询方法，可以查询用户是否存在，若不存在返回null

  > **UserMapper.java**

  ```java
  package com.cr.mapper;
  
  import com.cr.pojo.User;
  
  public interface UserMapper {
      User select(User user);
  }
  ```

- 创建UserMapper和mybatis映射文件 **UserMapper.xml**，路径是resource/com/cr/mapper

  > **UserMapper.xml**

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
  <mapper namespace="com.cr.mapper.UserMapper">
      <select id="select" parameterType="com.cr.pojo.User" resultType="com.cr.pojo.User">
          select * from t_user
          where username = #{username}
          and password = #{password}
      </select>
  </mapper>
  ```



### 8、服务层建立

- 建立UserService接口，位于service包下

  > **UserService.java**

  ```java
  package com.cr.service;
  
  import com.cr.pojo.User;
  
  public interface UserService {
      /**
       * 根据user信息检查数据库中是否存在该用户
       */
      User get(User user);
  }
  ```

- 在service包下建立impl子包，添加实现类UserServiceImpl

  > **UserServiceImpl.java**

  ```java
  package com.cr.service.impl;
  
  import com.cr.mapper.UserMapper;
  import com.cr.pojo.User;
  import com.cr.service.UserService;
  import org.springframework.stereotype.Service;
  
  import javax.annotation.Resource;
  // @Service用于业务层 功能等同于@component
  @Service("userService")
  public class UserServiceImpl implements UserService {
      @Resource
      private UserMapper userMapper;
  
      @Override
      public User get(User user) {
          // 通过Mapper的select方法查询用户
          return userMapper.select(user);
      }
  }
  ```

### 9、建立控制器

- 在controller包下建立控制器UserController，根据表单的用户名和密码，在数据中匹配，如果存在该用户，则跳转到登录成功界面**user.jsp**，如果用户不存在则返回null，重定向回登录界面，以便于重新登录。

> **UserController.java**

```java
package com.cr.controller;

import com.cr.pojo.User;
import com.cr.service.UserService;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.annotation.Resource;
// spring-mybatis.xml和springmvc-servlet.xml都配置了扫描控制层
@Controller
public class UserController {
    // 注入UserService
    @Resource
    private UserService userService;

    @RequestMapping(value = "/login")
    public String login(User user) {
        ModelAndView mv = new ModelAndView();
        System.out.println("开始查询---");
        user = userService.get(user);

        if (user != null) {
            System.out.println("查到的User: " + user.getUsername());
            mv.addObject("user", user);
            // 转到user.jsp用户界面
            return "user";
        } else {
            System.out.println("未查到此用户");
            // 查不到用户信息，则重定向回登录界面
            System.out.println("重定向回登录界面---");
            return "login";
        }
    }
}
```

### 10、建立Jsp页面

> **login.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>login</title>
</head>
<body>
<h1>用户登录界面</h1>
<h2>用户信息</h2>
<h3>Tom  123</h3>
<h3>Jack 456</h3>
<br>
<form id="form" action="/login" method="post">
    <table>
        <tr>
            <td>用户名</td>
            <td><input id="username" name="username" value="" /></td>
        </tr>
        <tr>
            <td>密码</td>
            <td><input id="password-always-checkbox" name="password" /></td>
        </tr>
        <tr>
            <td></td>
            <td align="right"><input type="submit" value="提交"></td>
        </tr>
    </table>
</form>

</body>
</html>
```



> **user.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>User</title>
</head>
<body>
<h1>用户登录成功</h1>
<br>
欢迎您: ${user}
</body>
</html>
```

