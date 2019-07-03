

<div style="background-color:silver;border:1px solid #000;width:130px;float:right;font-size:20px;font-weight:bold;">内部培训资料</div>
# Java后台开发常用框架及组件


本文将介绍一些在Java后台开发中常用的框架及组件，包含项目管理工具、开发集成框架、ORM框架、缓存访问组件等，使用它们能提高开发效率。此外，这些都是第三方开源软件，有兴趣的话还可以对其源代码进行研究。

## 1. 项目管理工具 - Maven

maven一个用于项目依赖与构建管理的工具，它能根据配置自动解析并引入相关的依赖jar，支持依赖关系的多层级解析，利用的构建功能，可以很容易完成项目的单元测试、打包、部署等。

一个maven项目的典型结构为：

```java
|____src                 //源代码根目录
|  |____main             //主体功能代码及资源根目录
|  |  |____resources     //资源文件根目录
|  |  |____java          //java代码根目录
|  |____test             //测试代码及资源根目录
|  |  |____resources     //测试资源文件根目录
|  |  |____java          //测试java代码根目录
|____target              //编译后的类及资源文件根目录
|  |____test-classes     //编译后的测试类及资源文件根目录
|  |____classes          //编译后的主体功能类及资源文件根目录
|____pom.xml             //工程管理文件
```



在介绍maven工作原理之间，先了解几个maven术语：

- <font face="黑体" size="4">POM文件</font>

maven工具使用一个固定名称的XML配置文件pom.xml来实现配置的集中管理，这个文件叫做 POM（Project Object Model）文件，以下就是一个简单的POM文件。

```xml

<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mfw.demo</groupId>
    <artifactId>first_maven_project</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>
    </dependencies>
</project>
```
- <font face="黑体" size="4">maven远程资源库</font>

一个远程集中式的软件仓库，所有使用maven进行管理的项目对外发布后，都存放于此。它分为三种，官方仓库（包括各种官网镜像）、私有仓库。
- <font face="黑体" size="4">maven本地资源库</font>

当使用maven进行项目开发时，maven会把用到的远程资源库中的软件包自动下载到本地一个文件夹，例如/home/test1/.m2/repository，以便下次直接使用，这个目录就叫做本地资源库。
- <font face="黑体" size="4">项目坐标</font>

定义一个项目在maven资源库的唯一标识，由组名、构件名、版本三者唯一标识。


### 1.1 依赖管理

Java后台项目开发中不可避免地会需要引入其它软件包（jar包)，如果没有自动化管理工具，需要手动获取到目标jar文件，并把它拷贝到指定的引用路径中，才能被正常引用，这当然是重复性地繁琐工作。使用maven后，只需要在在工程根目录添加POM文件，并在其中使用<dependency>标签声名所要引入jar包坐标（<groupId>、<artifactId>、<version>），就能唯一定位这个包，例如

```xml
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.9</version>
    </dependency>
```
如果这个在本地不存在 ，将由maven自动下载到本地。至于<groupId>、<artifactId>、<version>是每个maven项目都必须具有的唯一标识，在上面的pom文件中，也定义了自身的坐标。

### 1.2 构建管理

maven在项目构建管理方面做得很完善，从maven的视角来看，一个项目的生命周期（lifecycle）可以分别编译、测试、打包、安装、部署五个阶段，这些阶段分别使用compile、test、package、install、deploy命令来执行，它们之间是顺序依赖的关系，即后面的命令执行时会自动执行它之前的所有命令，只有前面的命令执行成功才会接着后续执行。

![avatar](https://s2.ax1x.com/2019/06/26/ZexHHJ.md.png)

图1 Maven项目生命周期

compile：编译工程主体功能src/main/目录下代码并将结果输出到target目录。

test：执行src/test/java目录中的测试用例，依赖于主体功能代码编译通过以后才能进行。此步可使用构建参数跳过。

package：将编译后结果按格式（默认为jar）打包至target目录。

install：将package步骤中生成的jar包安装到本地资源库中。

deploy：将package步骤中生成的jar包部署到maven远程资源库。

以上生命周期名称即相应maven执行命令，如compile周期对应的命令即为compile。

此外，利用maven提供的丰富插件（包括很多第三方插件），能够使用简单的命令就可以完成很多复杂的任务，例如包组装、代码检测、文档生成等这里就不一一赘述。



## 2. 开发集成框架 - Spring

Spring可以说是Java生态第一开发框架了，它经历了近20年的发展，已经形成了涵盖系统集成、控制反转（IoC，也叫依赖注入）容器、AOP管理、Web后台开发、应用启动管理、微服务管理等领域的技术生态，它为企业级应用开发提供了完整解决方案。限于篇幅，本文只介绍Spring IoC容器、Spring Boot和Spring MVC框架三部分。

### 2.1 Spring IoC 容器

IoC容器是一种实现了对象自动创建并建立相互依赖关系容器，因此它又可以称作依赖注入。IoC容器负责根据配置或者注解定位、解析、实例化应用程序中的用到的对象并建立这些对象间的依赖关系，这样应用程序就无需直接在代码中创建相关的对象，只需直接使用即可。

以下是一个采用XML配置进行依赖关系管理的例子：

```java
package demo.ioc;

public interface IHello {
    void sayHello();
}
```

```java
package demo.ioc;

public class TextHelloImpl implements IHello{

    @Override
    public void sayHello() {
        System.out.println("say hello by text.");  
    }

}
```

```java
package demo.ioc;

public class HelloClient {

    private String version;
    private IHello helloService;
    
    public void showVersion() {
        System.out.println("Version is " + getVersion());
    }

    public void textHello() {
        helloService.sayHello();
    }

   /**setter和getter方法是必不可少的，Spring IoC使用它们对属性进行访问*/
    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }

    public IHello getHelloService() {
        return helloService;
    }

    public void setHelloService(IHello helloService) {
        this.helloService = helloService;
    }

}
```

在src/main/java/demo/ioc目录下创建一个名为applicationContext.xml的文件（文件名可随意），内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.1.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd">
	
	<bean id="textHelloService" class="org.springlearning.ioc.TextHelloImpl"/>

	<bean id="helloClient" class="org.springlearning.ioc.HelloClient">
		<property name="version" value="1.4" />
		<property name="helloService" ref="textHelloService" />
	</bean>
	
</beans>
```

以上是对Java对象关系的依赖注入进行了配置，配置了一个id为textHelloService的JavaBean，和一个id为helloClient的JavaBean，其中helloClient对象有两个属性，分别使用常量值和引用值进行了注入（赋值），这样我们就可以直接使用注入好的helloClient对象了，下面编写使用代码：

```java
package demo.ioc;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Launcher {
    
    public static void main(String[] args) {
       
       try (ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext(
    		   new String[]{"demo/ioc/applicationContext.xml"});
       ) {
           HelloClient helloClient = ac.getBean("helloClient", HelloClient.class);
           helloClient.showVersion();//输出 Version is 1.4
           helloClient.textHello();//输出 say hello by text.
       }
       
    }
    
}
```



### 2.2 Spring Boot

SpringBoot一个高度集成的Java应用程序开发管理框架，它实现应用配置集中化管理、源码（包括依赖包）打包、启动、以及集成了许多开箱即用的组件（Servlet容器、数据库连接池、缓存、ES等），使得开发过程变得容易。

在上节的例子中，我们在完成IoC依赖注入后使用手动编程的方式获取并使用了helloClient对象，如果借助Spring Boot的自动依赖扫描与注入功能，是可以自动获得一个注入好的helloClient对象的，示例代码如下：



### 2.3 Spring MVC

Spring MVC框架是Web MVC设计模式的Java版本的实现，它提供一种基于HTTP协议请求/响应交互模型的轻量级Web开发架构，它实现了模型（M）、视图（V）、控制器（C）层的职责分离和系统解耦，与Spring框架无耦合，并能无缝集成到Servlet容器中。这种基于请求驱动类型的MVC框架把一些Web通用处理逻辑（如参数接收、验证、结果返回等）在框架层面进行了抽象封装，使得开发者无须对这些Web层的固化流程进行重复性编码，只需遵照它的规则去开发特定业务逻辑，从而简化了Web层的开发。

下面演示如何使用MVC开发基于HTTP 协议的API，



## 3. 单元测试框架 - JUnit

单元测试是程序开发中重要的环节，它不仅能在开发过程帮助我们尽早发现错误，确保代码正确性。Java中比较常用的单元测试框架为JUnit。它能通过非常简单的注解方式，快速对已编写的代码进行单元测试。

在SpringBoot中使用JUnit非常简单，只须在POM文件中加入：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <version>2.1.1.RELEASE</version>
    <scope>test</scope>
</dependency>
```

下面编写一下测试类src/test/java/demo/SpringBootJUnitTest.java：

```java
package demo;

import org.junit.After;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Ignore;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest({"spring.profiles.active=dev"})
public class SpringBootJUnitTest {


    @Before
    public void setUp() throws Exception {
        System.out.println("before running test");
    }


    @After
    public void tearDown() throws Exception {
        System.out.println("after running test");
    }
    

    @Test
    public void testIntEq(){
        Assert.assertEquals(129, 128);
        System.out.println("testIntEq pass");;
    }
    
//    @Ignore("not ready yet")
    @Test
    public void testSameString(){
        Assert.assertSame("这两个字符串不相等","abc","abc");
        System.out.println("testSameString pass");;
    }
    
}
```

运行可以看到以下结果。

```
before running test
after running test
before running test
testSameString pass
after running test
```

以下篇节有些例子会使用JUnit来运行。


## 4. ORM框架 - MyBatis
MyBatis 是管理Java对象与数据库表之间映射关系的优秀的持久层框架。它通过将 Java 接口和 POJOs(Plain Old Java Objects)映射成数据库中的记录的方式来完成对数据库的基本操作，例如CRUD等。MyBatis会自动将接收到的POJOs解析成SQL参数、设置SQL参数、执行SQL语句以及获取结果集并封装成相应对象，使用者不需要对这些过程进行编码，从而大大简化了数据库的访问。MyBatis 支持XML和注解配置方式。

### 4.1 代码自动生成器
mybatis generator  是一个自动化生成MyBatis所需 Java 接口（Mapper）和 POJOs和工具，如何使用它生成POJOs和Java接口（Mapper）？有命令行手动执行和IDE插件自动生成两种方式，为简便起见，这里介绍与IDE（Eclipse）集成的方式，首先安装插件

Eclipse菜单栏 -> Help -> Eclipse Marketplace，搜索框输入MyBatis Generator，在列表结果中点击install按钮，直至完成安装，重启Eclipse。

接下来编写配置文件，在Maven工程目录/src/main/resources/下创建mybatis-generator-config.xml文件，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN" "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    
    <classPathEntry location="/Applications/Eclipse.app/Contents/MacOS/~/.m2/repository//mysql/mysql-connector-java/8.0.13/mysql-connector-java-8.0.13.jar"/> <!--mysql jdbc包位置，按实际情况更改 -->
    
    <context id="mysql" defaultModelType="flat">

        <commentGenerator>
            <property name="suppressAllComments"  value="true"/>
        </commentGenerator>

        <!-- jdbc连接 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://192.168.1.100:3306/test"
                        userId="root" password="123456"/><!--mysql jdbc参数，按实际情况更改 -->

        <!-- 生成实体类 -->
        <javaModelGenerator targetPackage="com.hd.agr.service.dao.entity"
                            targetProject="demo-service"><!--生成的POJOs位置，按实际情况更改 -->
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="false"/>
         </javaModelGenerator>

        <!-- 生成dao接口 -->
        <javaClientGenerator targetPackage="com.mafengwo.content.demo.api.dao.mapper"
                             targetProject="cc-server-demo-api" type="ANNOTATEDMAPPER">
            <property name="enableSubPackages" value="true"/><!--生成的mapper位置，按实际情况更改 -->
        </javaClientGenerator>

        <!-- 配置需要生成代码的表信息， tableName 和domainObjectName 按实际情况更改,这里不生成帮助类examples-->
        <table schema="" tableName="user_info" domainObjectName="UserInfoDO"
               enableCountByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" enableUpdateByExample="false"/>
    </context>

</generatorConfiguration>
```

然后在该配置文件上点击右键选择 Run As -> Run MyBatis Generator，如果一切顺利，将会在控制台输出"MyBatis Generator Finished"成功提示，这时打开配置的目标代码目录就可以看到相应的代码了。

### 4.2 使用Mapper访问数据库
以与Spring Boot集成的方式，使用mysql数据库为例说明，添加以下maven依赖：

```xml
  <dependency>  
        <groupId>org.mybatis.spring.boot</groupId>  
        <artifactId>mybatis-spring-boot-starter</artifactId>  
        <version>1.3.0</version>  
  </dependency>
  <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.39</version>
  </dependency>
```

 在Maven工程目录/src/main/resources/application.properties加入以下配置：

```properties
spring.datasource.url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8
spring.datasource.username: root
spring.datasource.password: 123456
spring.datasource.driver-class-name: com.mysql.cj.jdbc.Driver
```

现在就可以编写数据库操作代码了，

```java
public class UserInfoDOMapperTest {
    
    @Autowired
    private UserDOMapper userDOMapper;

    //按id查询用户
    public void testSelectById() {
        UserDO userDO = userDOMapper.selectById(8733L);
        System.out.println(userDO);
    }
  
  	//插入用户
    public void testInsert() {
        UserDO record = new UserDO();
        record.setAccount("test002");
        record.setLastModifyTime(new Date());
        record.setNickname("test002");
        record.setSex(1);
        record.setUserId(IDGenerator.next());
        System.out.println(record);
        int effectedRow = userDOMapper.insert(record);
        System.out.println(effectedRow);
    }

}
```

以上代码中Mapper中的方法是代码生成器自动生成的，无需手工编写，如果想实现其它方法，只需按照规则在Mapper中添加相应的接口注解及SQL即可。

## 5. 缓存访问

##6. 序列化

在对象传输和存储之前需要对之进行序列化，Java平台的序列化方式有很多，如Native(ObjectInputStream/ObjectOutputStream)、JacksonJSON、Protobuf、Kryo、Fst，它们的使用方式和序列化性能差别很大，具体开发中可根据应用场景选择，这里介绍一种应用广泛的序列化方式——JacksonJSON，它也是Spring框架中默认的序列化工具。

##7. 字符串处理

##8. 编解码


##9. 日志组件


