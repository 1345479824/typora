# 一、springboot入门

## 1、项目中的Group和Artifact分别是什么

groupid和artifactId被统称为“坐标”是为了保证项目唯一性而提出的，如果你要把你项目弄到maven本地仓库去，你想要找到你的项目就必须根据这两个id去查找。

**groupId一般分为多个段，这里我只说两段，第一段为域，第二段为公司名称**。域又分为org、com、cn等等许多，其中org为非营利组织，com为商业组织。举个apache公司的tomcat项目例子：这个项目的groupId是org.apache，它的域是org（因为tomcat是非营利项目），公司名称是apache，artigactId是tomcat。

比如我创建一个项目，我一般会将groupId设置为cn.zr，cn表示域为中国，zr是我个人姓名缩写，**artifactId设置为testProj，表示你这个项目的名称是testProj**，依照这个设置，你的包结构最好是cn.zr.testProj打头的，如果有个StudentDao，它的全路径就是cn.zr.testProj.dao.StudentDao

**注：artifactId实际应该是项目名-模块名，不只是项目名**



## 2、目录结构（使用springboot initializer快速创建的spring boot项目）

1、resources文件夹中的目录结构

- static：保存所有的静态资源； js css images；
- templates：保存所有的模板页面（springboot默认jar包使用嵌入式的tomcat，默认不支持jsp页面），可以使用模板引擎（freemarker、thymeleaf）
- application.properties:springboot应用的配置文件，可以修改一些默认的设置

# 二、配置文件

## 1、yml配置例子

```yml
server:
	port: 8081
```

## 2、properties配置例子

```properties
server.port=8081
```

## 3、yml语法

#### 1、基本语法

k:(空格)v:表示一对键值对（空格必须有）；

以空格的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的，属性和值是大小写敏感的。

#### 2、值得写法

**字面量：普通的值（数字，字符串，布尔）**

k: v  字面直接来写；字符串默认不用加上单引号或者双引号；

“”：双引号不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思

​    name:"zhangsan \n lisi"   输出 zhangsan 换行 lisi

‘’：单引号会转义特殊字符

  name:"zhangsan \n lisi"   输出 zhangsan \n lisi

**对象和Map的写法**

```yaml
friends:
	lastName: zhangsan
	age: 20
```

行内写法

```yaml
friends: {lastName: zhangsan,age: 18}
```

**数组（List、set）**

用-值表示数组中的一个元素

```yaml
pets:
 - cat
 - dog
 - pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```

## 4、将yml文件中的配置注入一个bean中的步骤

**1、yml文件**

```yaml
friends:  
  lastName: zhangsan
  age: 20	
```
**2、javabean**

```java
/**
	@configurationProperties:告诉springboot将所有属性和配置文件中相关的配置进行绑定，默认是从全局配置文件中获取值
	prefix = "friends": 和配置文件下的friends下的所有属性进行映射
	
	@component: 将改bean加入容器，只有加入容器，才能使用容器提供的@configurationProperties注解
**/

@component
@configurationProperties(prefix = "friends")
public class Friend{
	private String lastName;
    private int age;
    //还需要再实现lastName和age的set方法，偷懒，就不写了。
}
```

**3、导入配置文件处理器，以后编写配置就有提示了**

```xml
	<dependency>
		<groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
	</dependency>
```

**4、在test包下面的ApplicationTest文件进行测试**

```java
	@autoWire
	Friend friend;
```

## **5、将properties文件的配置注入一个bean的步骤**

其它和yml配置文件一样,需要注意，idea默认的编码是utf-8，但是properties文件不是，所以要将properties文件改成utf-8编码，否则会中文乱码

```properties
friends.lastName=张三
friends.age=11
```

## 6、使用@value注解进行bean的注入

此时不需要@configurationProperties注解

```java
@Component
public class Friend{
    @Value("${friends.lastName}")  //从yml文件中获取lastName的值
	private String lastName;
    //@Value("11")  直接对age赋值为11
    @Value("#{11*2}") //进行算术表达式的运算
    private int age;
    //还需要再实现lastName和age的set方法，偷懒，就不写了。
}
```

## 7、@Value和@ConfigurationProperties注入属性的比较

|                  | @ConfigurationProperties | @Value     |
| ---------------- | ------------------------ | ---------- |
| 松散绑定         | 支持                     | 不支持     |
| 批量注入         | 支持                     | 不支持     |
| el表达式         | 不支持                   | 支持       |
| JSR303数据校验   | 支持                     | 不支持     |
| 复杂类型封装的值 | 支持获取                 | 不支持获取 |

**JSR303数据校验示例**

```java
@component
@configurationProperties(prefix = "friends")
@Validated
public class Friend{
    @Email   //指明注入的值必须要email格式，否则报错
	private String lastName;
    private int age;
    //还需要再实现lastName和age的set方法，偷懒，就不写了。
}
```

## 8、加载指定配置文件（@PropertySource&@ImportResource）

**1、@PropertySource**（加载指定配置文件对bean进行注入）

```java
@PropertySource(value = {"classpath:friends.properties"}) //加载指定配置文件
@component
@configurationProperties(prefix = "friends")
public class Friend{
	private String lastName;
    private int age;
    //还需要再实现lastName和age的set方法，偷懒，就不写了。
}
```

```java
@Autowire
ApplicationContext applicationContext； //获取容器，然后可以对Friend的Bean进行获取
```

**2、@ImportResource**（导入spring的配置文件，需要加在某个配置类上，可以加在主配置类上，main方法的那个类）

```java
@ImportResource(locations = {"classpath:beans.xml"})
```

**3、**springboot推荐给容器中**添加组件的方式：用配置类（全注解的方式）**，不用xml配置文件

将config包下的MyAppConfig申明为一个配置类

```java
@Configuration //指定当前类是一个配置类；可以用来替代spring配置文件
public Class MyAppConfig{
    //将方法的返回值添加到容器中，方法名就是这个组件（这个bean）的默认id
	@Bean
    public Friend friend(){
        return new Friend();
    }
}
```

## 9、配置文件占位符

**1、随机数**

```properties
friends.lastName=张三${random.value}${random.int}
friends.age=11
```

2、占位符获取之前配置过的值，如果没有可以使用：指定默认值

```properties
friends.lastName=张三${random.value}${random.int}
friends.age=11_${friends.lastName}_${friends.address:杭州}
```

## 10、Profile

**1、多Profile文件**

我们在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml

默认使用application.properties的配置

**2、激活指定的Profile文件**

在application.properties加配置

```properties
#使开发环境的properties配置文件生效，文件名为application-dev.properties
spring.profiles.active = dev 
```

```properties
#使生产环境的properties配置文件生效，文件名为application-prod.properties
spring.profiles.active = prod
```

**3、yml的多文档快方式**

```yaml
server:
  port: 8080
spring:
  profiles: 
    active: prod   #激活生产环境的文档块
---
server:
  port: 8081
spring:
  profiles: dev
---
server:
  port: 8082
spring:
  profiles: prod
```

**4、使用命令行的方式激活profile；用虚拟机参数激活**

可以百度了解

## 11、properties或者yml文件加载优先级

1、file:/config/

2、file:/

3、classpath:/config/                   //resouce文件夹下的config目录

4、classpath:/

优先级从高到底，所有位置的文件都会被加载，**高优先级配置内容会覆盖低优先级配置的内容**

# 三、自动配置原理

## 1、@EnableAutoConfiguration

autoConfiguration的解析<https://blog.csdn.net/zxc123e/article/details/80222967>

1、 SpringBoot启动的时候加载主配置类，并且利用**@EnableAutoConfiguration**注解开启了自动配置功能

2、@EnableAutoConfiguration的作用：

- 利用EnableAutoConfigurationImportSelector给容器中导入一些组件，可以查看selectImports（）方法的内容
- List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取候选的配置
- 通过SpringFactoriesLoader.loadFactoryNames()扫描所有jar包类路径下  META-INF/spring.factories,把扫描到的这些文件的内容包装成properties对象，从properties中获取到EnableAutoConfiguration.class类名对应的值，然后把他们添加到容器中

<font color="red">简单的说就一句话，@EnableAutoConfiguration将类路径下 META-INF/spring.factories里面配置的符合条件的**EnableAutoConfiguration的值**（**XXXXAutoConfiguration自动配置类**）都加入到了容器中，自动配置类会完成自动配置。</font>

## 2、@Conditional（条件的意思）派生注解

| @Conditional扩展注解       | 作用（判断是否满足当前指定条件） |
| -------------------------- | -------------------------------- |
| @ConditionalOnJava         | 系统的java版本是否符合要求       |
| @ConditionalBean           | 容器中存在指定的Bean             |
| @ConditionalOnMissingBean  | 容器中不存在指定的Bean           |
| @ConditionalOnClass        | 系统中有指定的类                 |
| @ConditionalOnMissingClass | 系统中没有指定的类               |
| @ConditionalOnProperty     | 系统中指定的属性是否有指定的值   |
| @ConditionalOnResource     | 类路径下是否存在指定资源文件     |
| 还有很多，就不写了。。     |                                  |

**自动配置类要在一定条件下才能生效**

我们可以通过debug=ture属性（在Properties或者yml文件中配置）来让控制台打印自动配置报告，这样我们就可以知道哪些自动配置类生效

## 3、EnableConfigurationProperties

作用：使使用 @ConfigurationProperties 注解的类生效。

说明：如果一个配置类只配置@ConfigurationProperties注解，而没有使用@Component，那么在IOC容器中是获取不到properties 配置文件转化的bean。说白了 @EnableConfigurationProperties 相当于把使用  @ConfigurationProperties 的类进行了一次注入。

链接：https://www.jianshu.com/p/7f54da1cb2eb

# 四、日志

## 1、日志的概念

日志的抽象层：SLF4J；

日志对应的实现层：Log4j或者Logback      Logback要高级点

SpringBoot底层是spring框架，Spring框架默认用的JCL日志抽象层，而SpringBoot选用SLF4J和Logback

## 2、日志的使用

```java
Logger logger = LoggerFactory.getLogger(getClass());
@Test
public void contextLoads(){
    //日志的级别 trace<debug<info<warn<error
    //可以通过properties或者yml文件调整指定包下日志输出的级别（logging.level.com.atguitu=trace）；日志就只会在这个级别以后的高级别生效
    logger.trace("这是trace日志");
    logger.debug("这是XXX")
    //SpringBoot默认使用的是info级别的日志，默认之后输出info、warn、和error的日志
    logger.info("这是XXX")
    logger.warn("这是XXX")
    logger.error("这是XXX")
       
}
```

# 五、Web开发

## 1、SpringBoot对静态资源的映射规则

1、所有/webjars/**,都去classpath:/META-INF/resources/webjars/目录找资源,可以用localhost:8080/webjars/jquery/3.3.1/jquery.js访问webjar目录下的资源

webjars:以jar包的方式引入静态资源

```xml
<!--引入jquery-webjar-->
<dependency>
	<groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>3.3.1</version>
</dependency>
```

![1607931441702](E:\Typora笔记\springboot\assets\1607931441702.png)

2、SpringBoot默认存放静态资源的文件夹（可以被访问到）

```
//classpath指的是类路径，是指的java包或者resources文件夹
classpath:/META-INF/resources/
classpath:/resources/
classpath:/static/
classpath:/public/
/   : 当前项目根路径
```

localhost:8080/a.js  , springboot会去静态资源文件夹找a.js

3、把index.html页面放在静态资源文件夹下，springboot启动会自动加载

4、所有**/favicon.ico图标文件都是在静态资源文件下找，加了图标后的效果如图，最左边有个图标

![1607933022527](E:\Typora笔记\springboot\assets\1607933022527.png)