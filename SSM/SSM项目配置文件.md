### 1、Web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
    <display-name>Archetype Created Web Application</display-name>
    <!--设置监听器加载类路径下的配置文件-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!--配置中文乱码的过滤器,必须放在第一个-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!--使用rest风格的uri，将页面普通的post请求专为指定的delete或者put请求-->
    <!--<filter>-->
    <!--<filter-name>HiddenHttpMethodFilter</filter-name>-->
    <!--<filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>-->
    <!--</filter>-->
    <!--<filter-mapping>-->
    <!--<filter-name>HiddenHttpMethodFilter</filter-name>-->
    <!--<url-pattern>/*</url-pattern>-->
    <!--</filter-mapping>-->
    <!--
      配置spring的监听器，默认只加载WEB-INF目录下的applicationContext.xml配置文件
      若不修改默认的加载路径，则会找到WEB-INF的classes目录下面
      -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--<context-param>-->
    <!--<param-name/>-->
    <!--<param-value/>-->
    <!--</context-param>-->
    <!--配置前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--
          未配置该语句，则DispatcherServlet在第一次接收到请求之后创建对象
          配置该语句，服务器在启动时就创建DispatcherServlet对象
          springmvc.xml在服务器启动时被加载
          -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!--
      /：对根目录下的所有jsp文件进行拦截
      -->
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>

### 2、springMVC.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
                           http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/mvc
                           http://www.springframework.org/schema/mvc/spring-mvc.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">
    <!--开起注解扫描  context下的component-scan
        HelloController被创建成对象-->
    <context:component-scan base-package="com.dazuoye">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    <!--视图解析器对象-->
    <bean id="internalResourceViewRe" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

```xml
<!--&lt;!&ndash; 上传文件 &ndash;&gt;-->
<!--<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">-->
    <!--<property name="defaultEncoding" value="utf-8"/>-->
    <!--&lt;!&ndash; 最大内存大小 &ndash;&gt;-->
    <!--<property name="maxInMemorySize" value="10240"/>-->
    <!--&lt;!&ndash; 最大文件大小，-1为不限制大小 &ndash;&gt;-->
    <!--<property name="maxUploadSize" value="-1"/>-->
<!--</bean>-->
<!--配置自定义类型转换器-->
<!--<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">-->
    <!--<property name="converters">-->
        <!--<set>-->
            <!--<bean class="cn.itcast.utils.StringToDateConverter"></bean>-->
        <!--</set>-->
    <!--</property>-->
<!--</bean>-->
<!--将springmvc不能处理的请求交给tomcat，能支持springmvc更高级的一些功能，JSR303校验，快捷的ajax...映射动态请求-->
<!--<mvc:default-servlet-handler/>-->
<!--映射配置文件-->
<mvc:resources mapping="/images/**" location="/images/"/>
<mvc:resources mapping="/css/**" location="/css/"/>
<mvc:resources mapping="/js/**" location="/js/"/>
<mvc:resources mapping="/video/**" location="/video/"/>
<mvc:resources mapping="/jquery/**" location="/jquery/"/>

<!-- 文件上传 -->
<bean id="multipartResolver"
      class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 设置默认编码 -->
    <property name="defaultEncoding" value="utf-8"></property>
    <!-- 上传图片最大大小5M -->
    <property name="maxUploadSize" value="5242440"></property>
</bean>
<!--开启springmvc框架注解支持，使得@RequestMapping注解生效
    自动加载处理器映射器、处理器适配器
-->
<mvc:annotation-driven>
    <!--防止jason乱码-->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="utf-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```
</beans>

### 3、applicationContext.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">
    <!--开起注解扫描,只处理service和dao，control不需要-->
    <context:component-scan base-package="com.dazuoye">
        <!--controller的注解不扫描-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

```xml
<!-- 配置C3P0的连接池对象 -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/jsp_dazuoye?useUnicode=true&amp;characterEncoding=utf-8"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>
<!-- 配置SqlSession的工厂 -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--配置映射路径-->
    <property name="mapperLocations" value="classpath*:com/dazuoye/mappers/*.xml"></property>
</bean>
<!--配置事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!--开启spring对注解事务的支持-->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
<!-- 配置扫描dao的包 -->
<bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.dazuoye.dao"/>
</bean>
```
</beans>

