**必读：**1.里面的包路径改成自己的，检查每个文件是否有爆红，爆红说明有错误，全部不爆红就可以启动tomcat了，然后能正常启动后，就可以编写自己的代码了。

# SSM框架搭建

* Spring + SpringMVC + Mybatis

SpringMVC负责实现MVC设计模式，Mybatis负责数据持久层，Spring负责管理SpringMVC和Mybatis相关的对象的创建和依赖注入。

## 1. 创建Maven工程，pom.xml

```xml
<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
  </dependency>

  <!--servlet-->
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
  </dependency>

  <!--jstl-->
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
  </dependency>

  <!--spring-webmvc-->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.16.RELEASE</version>
  </dependency>

  <!--spring-tx事务-->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>4.3.16.RELEASE</version>
  </dependency>

  <!--spring-aspects-->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>4.3.16.RELEASE</version>
  </dependency>

  <!--spring-jdbc-->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.3.16.RELEASE</version>
  </dependency>

  <!--spring-context-->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.16.RELEASE</version>
  </dependency>

  <!--mybatis-->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.5</version>
  </dependency>

  <!--mybatis-spring-->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.1</version>
  </dependency>

  <!--mysql-->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.9</version>
  </dependency>

  <!--druid数据库连接池-->
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.12</version>
  </dependency>

  <!--lombok-->
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok-maven-plugin</artifactId>
    <version>1.18.12.0</version>
    <scope>provided</scope>
  </dependency>

  <!--slf4j日志信息-->
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.32</version>
  </dependency>

  <!--jackson-core-->
  <dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.0</version>
  </dependency>

  <!--jackson-databind-->
  <dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
  </dependency>

</dependencies>

<build>
  <finalName>SSMBuild</finalName>
  <resources>
    <resource>
      <directory>src/main/java</directory><!--所在的目录-->
      <includes><!--包括目录下的.properties,.xml文件都会扫描到-->
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>false</filtering>
    </resource>
  </resources>
</build>
```

## 2. web.xml 

* Spring监听器（期间要在main文件夹下创建java和resources，然后创建applicationContext-service.xml）
  * 配置全局的spring.xml配置文件
  * 配置ContextLoaderListener监听器
* SpringMVC中央调度器
  * servlet，内部参数，加载顺序
  * url路径设置为 *.do
* 字符集编码过滤器
* 加载静态资源(没用到)

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!--Spring启动-->
  <!--Spring 1.全局参数-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/applicationContext-service.xml</param-value>
  </context-param>

  <!--字符集编码过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
      <param-name>forceRequestEncoding</param-name>
      <param-value>true</param-value>
    </init-param>
    <init-param>
      <param-name>forceResponseEncoding</param-name>
      <param-value>true</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  <!--Spring 2.监听器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <!--注册SpringMVC中央调度器-->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring/applicationContext-web.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>

  <!--欢迎页面-->
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
</web-app>

```

## 3. Mybatis配置文件

* mybatis-config.xml
  * 打印sql日志信息
  * 配置别名
  * 配置sql映射文件位置
  * 创建项目的基本包结构（可以在搭建开始前做好）

<img src="SSM框架搭建.assets/image-20220206225353211.png" alt="image-20220206225353211" style="zoom:50%;" />

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--1.打印sql日志信息-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <!--2.配置别名-->
    <typeAliases>
        <package name="com.bjpowernode.domain"/>
    </typeAliases>

    <!--3.配置sql映射文件-->
    <mappers>
        <package name="com.bjpowernode.dao"/>
    </mappers>

</configuration>
```

## Spring配置文件

applicationContext-dao.xml 整合 mybatis

* 在resources下创建jdbc.properties log4j.properties 等

* 引入mybatis配置文件 property-placeholder
* 创建数据源对象
* 创建SqlSessionFactory对象
  * 指定上面数据源
  * 指定mybatis配置文件位置
* 动态扫描dao接口，为其创建实例对象 MapperScannerConfiger

jdbc.properties 

```xml
jdbc.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
jdbc.driver=com.mysql.jdbc.Driver
```

log4j.properties

```xml
log4j.rootLogger=DEBUG,Console
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=%d [%t] %-5p [%c] - %m%n
log4j.logger.org.apache=INFO
```

applicationContext-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
">

    <!--引入数据库配置文件-->
    <context:property-placeholder location="classpath:properties/jdbc.properties"/>

    <!--1.声明数据源对象DataSource-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="driver" value="${jdbc.driver}"/>
    </bean>

    <!--2.创建SqlSessionFactory对象-->
    <!--mybatis-spring整合-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--注入数据库连接池-->
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置MyBatis全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
    </bean>

    <!--3.动态扫描dao接口，并为其创建代理对象-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--配置一下代理的方式，手动有两种，一种是继承Support，一种是实例化Template对象-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!--扫描dao包的位置-->
        <property name="basePackage" value="com.bjpowernode.dao"/>
    </bean>
    
</beans>
```

applicationContext-service.xml

* 引入持久层配置文件
* 扫描service包
* 事务配置
* 配置通知（切面）
* 配置aop

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
">

    <!--1.引入持久层配置文件-->
    <import resource="applicationContext-dao.xml"/>

    <!--2.声明组件扫描器 -->
    <context:component-scan base-package="com.bjpowernode.service"/>

    <!--3.事务配置 指定完成事务的具体实体类 commit, rollback-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--指定数据源DataSource-->
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!--4.配置通知（切面）：指定方法使用的事务属性  id:自定义的通知名称  transaction-manager：事务管理器 -->
    <tx:advice id="myAdvice" transaction-manager="transactionManager">
        <!--指定方法的事务属性    name:方法名称  isolation：隔离级别  propagation：传播行为
                        rollback-for：回滚的异常类， 对于自定义的异常要使用全限定名称，系统的异常类可以名称
                        不配置 rollback-for其实Spring也会回滚
                    -->
        <tx:attributes>
            <tx:method name="save*" isolation="DEFAULT" propagation="REQUIRED" rollback-for="Exception"/>
            <tx:method name="update*" isolation="DEFAULT" propagation="REQUIRED" rollback-for=""/>
            <tx:method name="delete*" isolation="DEFAULT" propagation="REQUIRED" rollback-for=""/>
            <tx:method name="update*" isolation="DEFAULT" propagation="REQUIRED" rollback-for=""/>

            <!--除了上面的方法以外的，其他方法-->
            <tx:method name="*" propagation="SUPPORTS" read-only="true" />
        </tx:attributes>
    </tx:advice>

    <!--5.配置aop-->
    <aop:config>
        <!--1.配置切入点-->
        <aop:pointcut id="servicePt" expression="execution(* com.bjpowernode.service.*.*(..))"/>
        <!--2.事务增强对象(通知+切入点)-->
        <aop:advisor advice-ref="myAdvice" pointcut-ref="servicePt"/>
    </aop:config>

</beans>
```

applicationContext-web.xml    SpringMVC配置文件

* controller注解扫描
* 配置视图解析器
* 开启注解扫描
* 后续有登陆权限控制，文件上传大小控制

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--SpringMVC配置-->
    <!--1.controller注解扫描-->
    <context:component-scan base-package="com.bjpowernode.controller"/>

    <!--2.配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--3.声明注解驱动 将 Object 数据转化为 JSON 数据-->
    <mvc:annotation-driven/>

    <!--<mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            &lt;!&ndash;登陆操作放行&ndash;&gt;
            <mvc:exclude-mapping path="/settings/user/login.do"/>
            &lt;!&ndash;跳转到登陆页面&ndash;&gt;
            <mvc:exclude-mapping path="/settings/user/toLogin.do"/>
            <bean class="com.bjpowernode.crm.interceptor.LoginInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>-->

</beans>
```



# 文件头模板

* applicationContext-service.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
">
   
    ......
    
</beans>
```

* applicationContext-web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    ......
    
</beans>
```

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>
```

