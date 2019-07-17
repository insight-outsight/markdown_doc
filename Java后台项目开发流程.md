

<div style="background-color:silver;border:1px solid #000;width:130px;float:right;font-size:20px;font-weight:bold;">内部培训资料</div>
# Java后台项目开发流程


一般来说，Java后台项目开发流程包括工程搭建、代码结构组织、编码、单元测试、问题修复、集成测试、预发布、发布等几个典型过程。

看下面这个示意图：
![avatar](https://s2.ax1x.com/2019/07/16/ZbCmo4.png)

图1 Java后台项目开发流程图

下面说明每个流程的主要任务：

工程搭建：该步主要是创建一个满足应用类型的工程主体骨架，这一步需要确定项目的类型、依赖包等。例如，可以搭建使用Maven管理工具、基本Spring Boot框架，这些都需要在工程创建之初首先确定。此外，是否是一个Web项目，是否需要对外发布成组件，这些都是需要考虑的因素。

代码结构组织：该步主要是对工程代码的结构进行组织，包括模块、逻辑单元等层次划分。代码的组织结构能大体反映出项目的基本架构设计。对于Java后台项目而言，一般会有某种通用的划分原则，这就是按职责分层设计，大致可分为：数据访问层（DAL），服务层（Service），业务逻辑层（Business），应用层(Application)，实际应用中服务层与业务逻辑层可以合二为一，简化为三层，每一层对应的对象分别以xxxDAO，xxxService，xxxController命名。

编码：这步就是应用程序代码的设计与编写过程，关于基本原则可参考第一讲中关于代码设计原则及最佳实践部分。在编码之初就要考虑代码的内聚性及与其它模块的交互性，模块之间交互要尽可能少，并且交互接口要提前约定好。代码设计既要满足现有功能，又要有一定预见性，方便功能扩展。编码过程应该分层编写代码，每层单独进行测试。

单元测试：单元测试是指对代码的每个最小可测试单元进行测试，例如一个类/实例方法，通过使用一些单元测试工具如JUnit，可以方便进行这项工作。由于代码之间的往往存在依赖关系，因此在开发中，也应尽可能从底层代码开始编码，以便上层代码的编写与测试不受影响。对于因各种原因无法事先得到所依赖的代码的情况，也有相应的解决方案，那就是Mock测试，即可以伪造一个跟真实代码功能一样的对象，使上层代码的编写和测试不受限于底层代码的开发进度。单元测试的目的是使代码各部分都是可被测试的、可被反复测试、可被随时测试，以便错误及早被发现，由此保证代码各部分质量。

集成测试：集成测试顾名思义是同时对各代码单元，有时会包含多个系统进行测试，集成测试一般面向的是功能测试、性能评测，可以由人工操作来完成，也可以由自动化工具模拟用户行为来完成。集成测试是开发人员和测试人员都必须参与的工作，开发人员将集成测试通过后的代码提交给测试人员进行测试，这种测试性交付是开发人员和测试人员协同工作的纽带。

预发布：将开发环境测试通过后的代码发布到预发布环境，采用与线上环境一致的数据对代码各项功能进行验证来检查是否符合预期，这个过程预发布环境测试。预发布是代码上线前最后一项质量保证措施，需要开发人员和测试都参与确认。

发布：将代码发布到生产环境，实现最终交付。一般采用灰度发布的方案，先局部后全量，保证平稳上线。上线完成后需要开发人员和测试共同参与验证。

下面通过一个实际项目讲解Java后台项目的开发过程。

**软件环境**

- IDE：Eclipse
- 工程管理工具：Maven
- 开发框架：Spring Boot
- 数据库：MySQL
- 缓存：Redis

**项目功能**

实现用户信息管理HTTP API，包括注册、登录两个功能接口。



## 1. 项目搭建

在Eclipse中创建一个基于Maven的项目，取名为course3-demo，具体方法是

File -> New -> Project…，在对话框中选择Maven -> Maven Project，单击Next

![avatar](https://s2.ax1x.com/2019/07/16/ZbJ5g1.md.png)

在弹出的页面，勾选Create a simple project复选框，并选择Location，然后单击下一步

![avatar](https://s2.ax1x.com/2019/07/16/ZbJLUe.md.png)

在下图中的界面，输入GroupId和ArtifactId名称，单击Finish，完成工程结构创建。

![avatar](https://s2.ax1x.com/2019/07/16/ZbYFUg.md.png)




## 2. 引入项目依赖

完成工程创建后，需要根据实际项目类型，引入依赖包，这里我们是创建一个基于Spring Boot的工程，并引入一些需要的依赖包，如MyBatis，Jedis，而这只需要在pom.xml加入以下配置即可：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mafengwo.content.course3</groupId>
    <artifactId>course3-demo</artifactId>
    <version>1.0.0-SNAPSHOT</version>
		<!--继承一个父POM，自动使用其引入的依赖包-->
    <parent>
        <groupId>com.mafengwo.content.framework</groupId>
        <artifactId>springboot-skeleton</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <dependencies>
				<!--依赖一个工具包-->
        <dependency>
            <groupId>com.mafengwo.content.component</groupId>
            <artifactId>scooter</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>

    </dependencies>

    <build>
        <finalName>${project.artifactId}</finalName>
        <filters>
            <filter>${env.dir.name}.properties</filter>
        </filters>
    </build>

    <profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <env.dir.name>${basedir}/src/main/resources/env/dev</env.dir.name>
            </properties>
            <build>
                <resources>
                    <resource>
                        <directory>${basedir}/src/main/resources</directory>
                        <filtering>false</filtering>
                        <excludes>
                            <exclude>env/**</exclude>
                        </excludes>
                    </resource>
                    <resource>
                        <directory>${basedir}/src/main/resources</directory>
                        <filtering>true</filtering>
                        <includes>
                            <include>logback-spring.xml</include>
                        </includes>
                    </resource>
                </resources>
            </build>
        </profile>
        <profile>
            <id>test</id>
            <properties>
                <env.dir.name>${basedir}/src/main/resources/env/test</env.dir.name>
            </properties>
            <build>
                <resources>
                    <resource>
                        <directory>${basedir}/src/main/resources</directory>
                        <filtering>false</filtering>
                        <excludes>
                            <exclude>env/**</exclude>
                        </excludes>
                    </resource>
                    <resource>
                        <directory>${basedir}/src/main/resources</directory>
                        <filtering>true</filtering>
                        <includes>
                            <include>logback-spring.xml</include>
                        </includes>
                    </resource>
                </resources>
            </build>
        </profile>
    </profiles>


</project>
```




## 3. 工程代码结构
按照分层设计的原则，将工程按以下包结构进行组织，

![avatar](https://s2.ax1x.com/2019/07/17/ZqH0V1.png)


## 3. 编码
代码结构组织划分完成，就可以分层编写代码了，可以采取由下层到上层的编写顺序，依次实现DAO、Service和Controller。

### 3.1 创建一个数据库表

```sql
CREATE TABLE `t_arch_user_base_info` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键',
  `user_id` bigint(20) NOT NULL COMMENT '用户唯一标识',
  `user_name_type` int(11) NOT NULL COMMENT '用户名类型',
  `user_name` varchar(64) NOT NULL COMMENT '用户唯一名称',
  `nick_name` varchar(20) NOT NULL COMMENT '昵称',
  `gender` int(11) NOT NULL COMMENT '性别',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `last_modify_time` datetime NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `idx_user_id` (`user_id`) USING BTREE,
  UNIQUE KEY `idx_user_name` (`user_name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='user_base_info';
```



### 3.2 DAO层代码编写与单元测试

DAO层代码生成方法可参考第二讲关于代码自动生成工具的篇节，这里不再说明。完成后在com.mafengwo.content.course3.dao包下将会看到DO和Mapper对象。

代码生成工具能生成最基本的数据库操作，如果不满足要求，可以通过在DAO对象中（Mapper）自定义一些方法来实现相应功能，以下就是一个自定义的查询方法

```java
    @Select({
        "select",
        "id, user_id, user_name_type, user_name, nick_name, gender, create_time, last_modify_time",
        "from t_arch_user_base_info",
        "where user_name = #{userName,jdbcType=VARCHAR}"
    })
    @ResultMap("userBaseInfoResultMap")
    UserBaseInfoDO selectByUserName(String userName);
```
编写完DAO方法，下面就立即可以进行单元测试了，具体方法参考第二讲。

### 3.3 Service层代码编写与单元测试

首先简单介绍一下Service层，Service层的定位是一组逻辑功能单元接口的集合，一般按业务场景来组织，比如一个用户服务，可以包含登录、注册等功能接口。Service层的代码分为Service接口和Service实现。下面编写一个UserService和UserServiceImpl类。



```java
package com.mafengwo.content.course3.service;


import com.mafengwo.content.course3.exception.Course3DemoBizException;
import com.mafengwo.content.course3.service.dto.UserBaseInfoDTO;

public interface UserService {

    boolean add(UserBaseInfoDTO userDTO) throws Course3DemoBizException;
    UserBaseInfoDTO queryByUserName(String userName) throws Course3DemoBizException;

}

```

UserService接口定义了三个业务功能接口，分别对应添加用户、按用户名查询用户信息。其具体实现类为UserServiceImpl，代码如下：

```java
package com.mafengwo.content.course3.service;

import java.util.Date;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.mafengwo.content.component.scooter.DateUtils;
import com.mafengwo.content.component.scooter.IDGenerator;
import com.mafengwo.content.course3.dao.entity.UserBaseInfoDO;
import com.mafengwo.content.course3.dao.mapper.UserBaseInfoDOMapper;
import com.mafengwo.content.course3.exception.Course3DemoBizException;
import com.mafengwo.content.course3.service.dto.UserBaseInfoDTO;

@Service
public class UserServiceImpl implements UserService {

    private Logger logger = LoggerFactory.getLogger(UserServiceImpl.class);
    
    @Autowired
    private UserBaseInfoDOMapper userBaseInfoDOMapper;
  
    
    @Override
    public boolean add(UserBaseInfoDTO userDTO) throws Course3DemoBizException {
        UserBaseInfoDO userBaseInfoDO = new UserBaseInfoDO();
        userBaseInfoDO.setCreateTime(new Date());
        userBaseInfoDO.setGender(userDTO.getGender());
        userBaseInfoDO.setLastModifyTime(new Date());
        userBaseInfoDO.setNickName(userDTO.getNickName());
        userBaseInfoDO.setUserId(IDGenerator.next());
        userBaseInfoDO.setUserName(userDTO.getUserName());
        userBaseInfoDO.setUserNameType(userDTO.getUserNameType());
        return userBaseInfoDOMapper.insert(userBaseInfoDO) == 1;
    }

    private UserBaseInfoDTO toUserBaseInfoDTO(UserBaseInfoDO userBaseInfoDO) {
        UserBaseInfoDTO userBaseInfoDTO = new UserBaseInfoDTO();
        userBaseInfoDTO.setCreateTime(DateUtils.formatToStandardTimeString(userBaseInfoDO.getCreateTime()));
        userBaseInfoDTO.setGender(userBaseInfoDO.getGender());
        userBaseInfoDTO.setLastModifyTime(DateUtils.formatToStandardTimeString(userBaseInfoDO.getLastModifyTime()));
        userBaseInfoDTO.setNickName(userBaseInfoDO.getNickName());
        userBaseInfoDTO.setUserId(userBaseInfoDO.getUserId());
        userBaseInfoDTO.setUserName(userBaseInfoDO.getUserName());
        userBaseInfoDTO.setUserNameType(userBaseInfoDO.getUserNameType());
        return userBaseInfoDTO;
    }

    @Override
    public UserBaseInfoDTO queryByUserName(String userName) throws Course3DemoBizException {
        UserBaseInfoDO userBaseInfoDO = userBaseInfoDOMapper.selectByUserName(userName);
        if(userBaseInfoDO == null){
            return null;
        }
        return toUserBaseInfoDTO(userBaseInfoDO);
    }

}

```

完成Service层的代码后，也要单元测试，以保证代码正确性。测试过程不再表述。


### 3.4 Controller层代码编写与单元测试

上面已经实现DAO层和Service层的代码并进行了单元测试，如果一切正常，就可以编写Controller层代码了，Controller层负责HTTP API的请求/响应交互处理。

利用Spring MVC框架的相关组件很容易实现HTTP API接口，用户注册、登录的Controller代码如下：

```java
package com.mafengwo.content.course3.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.mafengwo.content.course3.controller.dto.LoginResponse;
import com.mafengwo.content.course3.controller.dto.LoginResponseResult;
import com.mafengwo.content.course3.controller.dto.SimpleOkayResponse;
import com.mafengwo.content.course3.controller.dto.UserRegisterRequest;
import com.mafengwo.content.course3.enumerate.GenderEnum;
import com.mafengwo.content.course3.enumerate.ResultCodeEnum;
import com.mafengwo.content.course3.exception.Course3DemoBizException;
import com.mafengwo.content.course3.service.UserService;
import com.mafengwo.content.course3.service.dto.UserBaseInfoDTO;


@RestController
@RequestMapping("/user")
public class UserController extends BaseController {

    private Logger logger = LoggerFactory.getLogger(UserController.class);
            
    @Autowired
    private UserService userService;

    /**
     * 注册
     * @throws Exception 
     */
    @PostMapping(value = "/register")
    public SimpleOkayResponse register(HttpServletRequest request, HttpServletResponse response,
            @RequestBody UserRegisterRequest userRegisterRequest,
            @RequestParam(value = "registerToken", required = true) String registerToken,
            @RequestHeader(value = "appType", required = true) Integer appType,
            @RequestHeader(value = "appVersion", required = true) Integer appVersion) throws Exception {
        
        logger.debug("userRegisterRequest:{},appType:{},appVersion:{}",userRegisterRequest,appType,appVersion);
        
        checkGender(userRegisterRequest.getGender());
        
        UserBaseInfoDTO userDTO = new UserBaseInfoDTO();
        userDTO.setGender(userRegisterRequest.getGender());
        userDTO.setNickName(userRegisterRequest.getNickName());
        userDTO.setUserName(userRegisterRequest.getUserName());
        userDTO.setUserNameType(userRegisterRequest.getUserNameType());
        
        boolean result = userService.add(userDTO);
        logger.debug("register status:{}",result);
        return new SimpleOkayResponse();
        
    }
    
    private void checkGender(Integer gender) throws Course3DemoBizException {
        if(gender == null || GenderEnum.valueOf(gender) == null) {
            throw new Course3DemoBizException(ResultCodeEnum.IllegalRequestArgument);
        }
    }

    /**
     * 登录
     * @throws Exception 
     */
    @PostMapping(value = "/login")
    public LoginResponse login(HttpServletRequest request, HttpServletResponse response,
            @RequestParam(value = "userName", required = true) String userName,
            @RequestHeader(value = "appType", required = true) Integer appType,
            @RequestHeader(value = "appVersion", required = true) Integer appVersion) throws Exception {
        
        logger.debug("userName:{},appType:{},appVersion:{}",userName,appType,appVersion);
        UserBaseInfoDTO userBaseInfoDTO = userService.queryByUserName(userName);
        LoginResponse loginResponse = new LoginResponse();
        LoginResponseResult loginResponseResult = new LoginResponseResult();
        if( userBaseInfoDTO != null ) {
            loginResponseResult.setNickName(userBaseInfoDTO.getNickName());
            loginResponseResult.setUserId(userBaseInfoDTO.getUserId());
            loginResponseResult.setUserName(userBaseInfoDTO.getUserName());
            loginResponseResult.setRegisterTime(userBaseInfoDTO.getCreateTime());
        }
        else {
            throw new Course3DemoBizException(ResultCodeEnum.UserNotExist);
        }
        loginResponse.setResData(loginResponseResult);
        logger.debug("resData:{}",loginResponse);
        return loginResponse;
        
    }
 

}

```

单元测试代码如下：

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

   /**setter和getter方法是必不可少的，Spring IoC使用它们对属性字段进行访问*/
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



完成Controller层代码之后，一个完整的Web 应用就可以运行起来对外提供服务了。

### 2.2 Spring Boot

SpringBoot一个高度集成的Java应用程序开发管理框架，它实现了应用配置集中化管理、源码（包括依赖包）打包、启动、以及集成了许多开箱即用的组件（Servlet容器、数据库连接池、缓存、ES等），使得开发过程变得容易。

在上节的例子中，我们在完成IoC依赖注入后使用手动编程的方式获取并使用了helloClient对象，如果借助Spring Boot的注解式自动扫描与依赖注入功能，是可以自动获得一个注入好的helloClient对象的，示例代码如下：

添加Maven依赖：

```xml
  <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
  </parent>

  <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    		<dependency>
    				<groupId>org.springframework.boot</groupId>
    				<artifactId>spring-boot-starter-test</artifactId>
    				<version>2.1.1.RELEASE</version>
    				<scope>test</scope>
				</dependency>
  <dependencies>  
```

编写代码：

```java

@Component//访注解表示该类在IoC启动后注册成一个Spring Bean组件
public class TextHelloImpl implements IHello{
// ... 同上
}
```

```java
@Component
public class HelloClient {
    @Value("1.2")
    private String version;
    @Autowired
    private IHello helloService;
		// ... 同上
}
```

在最上层包目录添加一个SpringBoot启动类：

```java
package demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplicationBootStrap {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplicationBootStrap.class, args);
        System.out.println("DemoApplicationBootStrap started");
    }
    
}

```

在src/test/java/demo/ioc下创建JUnit测试类，

```java
package demo.ioc;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootIoCTest {
    
    @Autowired
    private HelloClient helloClient;
  
    @Test
    public void testAnnotationedIoC(){
        System.out.println(helloClient.getVersion());;
        helloClient.textHello();;
    }
    
}
```

在IDE中类中右键，Run，如果一切顺利，在控制台将看到以下结果：

```

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.1.RELEASE)

1.2
say hello by text.
```

这样，一个简单的程序借助于Spring框架便运行起来了。

### 2.3 Spring MVC

Spring MVC框架是Web MVC设计模式的Java版本的实现，它提供一种基于HTTP协议请求/响应交互模型的轻量级Web开发架构，它实现了模型（M）、视图（V）、控制器（C）层的职责分离和系统解耦，与Spring框架无耦合，并能无缝集成到Servlet容器中。这种基于请求驱动类型的MVC框架把一些Web通用处理逻辑（如参数接收、验证、结果返回等）在框架层面进行了抽象封装，使得开发者无须对这些Web层的固化流程进行重复性编码，只需遵照它的规则去开发特定业务逻辑，从而简化了Web层的开发。

下面演示如何使用MVC开发基于HTTP 协议的API，首先来配置MVC，添加如下配置类：

```java
package demo;

import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.DispatcherServlet;
import org.springframework.web.servlet.config.annotation.PathMatchConfigurer;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

@Configuration
public class WebMvcCustomConfig extends WebMvcConfigurationSupport {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.setUseSuffixPatternMatch(true);
    }

    @Bean
    public ServletRegistrationBean<DispatcherServlet> dispatcherRegistration(
            DispatcherServlet dispatcherServlet) {
        ServletRegistrationBean<DispatcherServlet> servletRegistration = new ServletRegistrationBean<DispatcherServlet>(
                dispatcherServlet);
        servletRegistration.addUrlMappings("*.do");
        servletRegistration.addUrlMappings("*.html");
        return servletRegistration;
    }

    
}
```

然后编写一个仅仅返回“Hello !”文字的API，

```java
package demo.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping(value = "/hello", method = RequestMethod.GET)
    public String hello(HttpServletRequest request,HttpServletResponse response) {
        return "Hello !";
    }

}
```



创建Spring Boot配置文件src/main/resources/application.properties并添加

```properties
server.port = 8445
```

在TemplateApplicationBootStrap类右键，Run，如果一切顺利，在控制台将看到以下输出：

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.1.RELEASE)
```

这时，在浏览器地址栏输入http://localhost:8445/hello.do，即可看到“Hello !”文字。

Spring MVC在开发Web Pages应用上也非常方便，它能集成JSP（已过时，不推荐使用）、FreeMarker、Thymeleaf等页面模板技术。下面看一个使用Thymeleaf开发Web Pages的示例：

Maven添加以下依赖：

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

在application.properties添加以下配置：

```

# 是否启用缓存，建议生产环境开启
spring.thymeleaf.cache=false 
spring.thymeleaf.check-template-location=true 
# HTTP Header Content-Type 值
spring.thymeleaf.content-type=text/html 
spring.thymeleaf.enabled=true 
# 模版编码
spring.thymeleaf.encoding=UTF-8 
# 应该从解析中排除的视图名称列表（用逗号分隔）
spring.thymeleaf.excluded-view-names= 
# 模版模式
spring.thymeleaf.mode=HTML5 
# 模版存放路径
#spring.thymeleaf.prefix=classpath:/templates/ 
# 模版后缀
spring.thymeleaf.suffix=.html
```

编写Controller：

```java
package demo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/thymeleaf")
public class HiPageController {

    @GetMapping(value = "hiPage")
    public String hi(Model model){
        model.addAttribute("userName","xiaoming");
        return "hiPage";
    }
 

}
```

编写页面src/main/resources/templates/hiPage.html：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>SpringBoot Thymeleaf模版渲染</title>
</head>
<body>
    <h1>Hi,</h1>
    <p style="background-color:#33D589" th:text="'用户名称：' + ${userName}"/>
</body>
</html>
```

在TemplateApplicationBootStrap类右键，Run，这时，在浏览器地址栏输入http://localhost:8445/thymeleaf/hiPage.html，即看到一个欢迎页面。

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

下面编写一下测试类src/test/java/demo/junit/SpringBootJUnitTest.java：

```java
package demo.junit;

import org.junit.After;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Ignore;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
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
        System.out.println("testIntEq pass");
    }
    
//    @Ignore("not ready yet")
    @Test
    public void testSameString(){
        Assert.assertSame("这两个字符串不相等","abc","abc");
        System.out.println("testSameString pass");
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
mybatis generator  是一个自动化生成MyBatis所需 Java 接口（Mapper）和 POJOs和工具，如何使用它生成POJOs和Java接口（Mapper）？有命令行手动执行和IDE插件自动生成两种方式，为简便起见，这里介绍与IDE（Eclipse）集成的方式，首先安装插件，

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
                        connectionURL="jdbc:mysql://localhost:3306/test"
                        userId="root" password="123456"/><!--mysql jdbc参数，按实际情况更改 -->

        <!-- 生成实体类 -->
        <javaModelGenerator targetPackage="demo.dao.entity"
                            targetProject="first_maven_project"><!--生成的POJOs位置，按实际情况更改 -->
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="false"/>
         </javaModelGenerator>

        <!-- 生成dao接口 -->
        <javaClientGenerator targetPackage="demo.dao.mapper"
                             targetProject="first_maven_project" type="ANNOTATEDMAPPER">
            <property name="enableSubPackages" value="true"/><!--生成的mapper位置，按实际情况更改 -->
        </javaClientGenerator>

        <!-- 配置需要生成代码的表信息， tableName 和domainObjectName 按实际情况更改,这里不生成帮助类examples-->
        <table schema="" tableName="user_base_info" domainObjectName="UserBaseInfoDO"
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
        <version>8.0.13</version>
  </dependency>
```

 在Maven工程目录/src/main/resources/application.properties加入以下配置，实际参数值根据实际情况填写：

```properties
spring.datasource.url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8
spring.datasource.username: root
spring.datasource.password: 123456
spring.datasource.driver-class-name: com.mysql.cj.jdbc.Driver
spring.datasource.type = com.zaxxer.hikari.HikariDataSource
spring.datasource.hikari.maximum-pool-size = 300
spring.datasource.hikari.minimum-idle = 5
spring.datasource.hikari.connection-timeout = 3000
spring.datasource.hikari.read-only = false
spring.datasource.hikari.pool-name = templateHikariCP
```

添加Mapper扫描目录，

```java
package demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan(basePackages = "demo.dao.mapper")
public class DemoApplicationBootStrap {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplicationBootStrap.class, args);
        System.out.println("DemoApplicationBootStrap started");
    }
    
}
```

现在就可以编写数据库操作代码了，

```java
package demo.dao;


import static org.junit.Assert.assertEquals;

import java.util.Date;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import demo.dao.entity.UserBaseInfoDO;
import demo.dao.mapper.UserBaseInfoDOMapper;


@RunWith(SpringRunner.class)
@SpringBootTest
public class UserDaoTest {

    @Autowired
    private UserBaseInfoDOMapper userBaseInfoDOMapper;
    
    @Test
    public void testSelectByPrimaryKey() {
        UserBaseInfoDO userBaseInfoDO = userBaseInfoDOMapper.selectByPrimaryKey(6L);
        System.out.println(userBaseInfoDO.getId());
    }

    @Test
    public void testInsert() {
        UserBaseInfoDO record = new UserBaseInfoDO();
        record.setCreateTime(new Date());
        record.setGender(1);
        record.setLastModifyTime(new Date());
        record.setNickName("test022-ju");

        record.setUserId(1880002L);
        record.setUserName("arch022@gmail.com");
        record.setUserNameType(1);
        
        System.out.println(record);
        int effectedRow = userBaseInfoDOMapper.insert(record);
        System.out.println(effectedRow);
        assertEquals(1,effectedRow);
    }
    
}

```

以上代码中Mapper中的方法是代码生成器自动生成的，无需手工编写，如果想实现其它方法，只需按照规则在Mapper中添加相应的接口注解及SQL即可。

## 5. 缓存访问组件 - Jedis

在Spring Boot中已经集成好了访问Redis的组件Jedis、Lettuce，本文以Jedis使用为例进行说明。

添加 Maven依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <exclusions>  
                <exclusion>  
                    <groupId>io.lettuce</groupId>
                    <artifactId>lettuce-core</artifactId>
                </exclusion>  
            </exclusions>
        </dependency>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
        </dependency>
```

 在Maven工程目录/src/main/resources/application.properties加入以下配置：

```properties
spring.redis.host = localhost
spring.redis.port = 6379
spring.redis.database = 0

spring.redis.jedis.pool.max-active = 64
spring.redis.jedis.pool.max-wait = 1000ms
spring.redis.jedis.pool.max-idle = 8
spring.redis.jedis.pool.min-idle = 0
spring.redis.timeout = 2000ms
```

编写测试代码：

```java
import java.util.ArrayList;
import java.util.List;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootJedisTest {
    
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    
    @Test
    public void testJedis() throws Exception {
        stringRedisTemplate.opsForValue().set("j_s_100", "测试js100");
        List<String> list =new ArrayList<>();
        list.add("e1");
        list.add("e2");
        list.add("e3");
        stringRedisTemplate.opsForList().leftPushAll("j_s_list100",list);
        stringRedisTemplate.opsForList().range("j_s_list100",0,-1).forEach(value ->{
           System.out.println(value);
        });
    }
    
}
```



##6. 日志组件

日志是程序运行状态的记录，它能为开发者记录关键点运行时数据和异常堆栈信息，这对于开发者来说往往是至关重要的信息。这里介绍Java中常用的日志组件slf4j和logback组合，其中slf4j是一套外观模式（Facade）的日志接口，它后面可以对接多种不同的slf4j实现。如果使用logback，在Spring Boot工程中，只需要在src/main/resources下添加一个名为logback-spring.xml的文件，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="false" scanPeriod="60 seconds" debug="false">

    <property name="log_history_retain_days" value="100" /><!--定义一个按天分割的日志保留天数-->
    <property name="log_history_retain_hours" value="240" /><!--定义一个按小时分割的日志保留小时数-->
    <property name="log_dir" value="/your/log/path/" /><!--存放日志的路径-->
    <property name="logLevel" value="debug" /><!--日志的默认级别-->
    <property name="log_pattern" value="%d{MM-dd HH:mm:ss} [%thread] %-5level [%logger.%M %line] - %msg%n%ex" /><!--日志的格式-->

    <appender name="all" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
        <file>${log_dir}/all.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log_dir}/all.log.%d{yyyy-MM-dd}</fileNamePattern>
            <maxHistory>${log_history_retain_days}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log_pattern}</pattern>
        </encoder>
    </appender>

    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!-- encoder 默认配置为PatternLayoutEncoder -->
        <encoder>
            <pattern>${log_pattern}</pattern>
        </encoder>
    </appender>

    <appender name="debug" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>DEBUG</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${log_dir}/debug.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log_dir}/debug.log.%d{yyyy-MM-dd-HH}</fileNamePattern>
            <maxHistory>${log_history_retain_hours}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log_pattern}</pattern>
        </encoder>
    </appender>

    <appender name="info" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${log_dir}/info.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log_dir}/info.log.%d{yyyy-MM-dd}</fileNamePattern>
            <maxHistory>${log_history_retain_days}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log_pattern}</pattern>
        </encoder>
    </appender>

    <appender name="warn" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${log_dir}/warn.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log_dir}/warn.log.%d{yyyy-MM-dd}</fileNamePattern>
            <maxHistory>${log_history_retain_days}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log_pattern}</pattern>
<!--            <immediateFlush>false</immediateFlush> -->
        </encoder>
    </appender>

    <appender name="error" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${log_dir}/error.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log_dir}/error.log.%d{yyyy-MM-dd}</fileNamePattern>
            <maxHistory>${log_history_retain_days}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log_pattern}</pattern>
        </encoder>
    </appender>
    <!-- name根据实际情况填写工程最顶层包的名称，以便所有类均可被该日志配置覆盖到，level定义了最低应该
输出的日志级别，比如设置level=info，那么info级别以下（debug)的日志将不会打印-->
    <logger name="demo.log" level="${logLevel}" additivity="false">
        <appender-ref ref="debug" />
        <appender-ref ref="info" />
        <appender-ref ref="warn" />
        <appender-ref ref="error" />
    </logger>

    <root level="${logLevel}">
        <appender-ref ref="all" />
    </root>

</configuration>
```

在以上配置中，定义了6个日志输出器，它们种类分两种，一种是RollingFileAppender，一种是ConsoleAppender。RollingFileAppender代表将日志输出到文件，并可根据策略自动切割，而ConsoleAppender是将日志输出的标准输出设备（stdout)。

在logback中将日志按级别主要分别debug, info,warn,error几种，每种日志一般用于输出不同重要程度的信息，分别使用debug(),info(),warn(),error()方法打印。上面配置文件中的配置是设定debug(),info(),warn(),error()方法打印的内容分别保存到debug.log，info.log，warn.log，error.log文件中。实际中一般会根据需要灵活设置日志的最低打印级别，使低于这个级别的日志将不会被打印，即使在程序中调用日志打印方法。一般开发环境设置debug，线上设置info，这样在程序中加入的debug()方法打印代码，在上线后不用清除也不会打印内容。

以下代码演示如何打印日志：

```java
package demo.log;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootLogbackTest {
    
    private Logger logger = LoggerFactory.getLogger(SpringBootLogbackTest.class);
    
    @Test
    public void testLogback() throws Exception {
        logger.debug("This is {} level msg","debug");
        logger.info("This is {} level msg","info");
        logger.warn("This is {} level msg","warn");
        logger.error("This is {} level msg","error");
    }
    
}
```

运行，现在去/your/log/path/下对应的文件看打印的内容吧，是不是debug.log没有相应的内容？把配置文件改成 :

```xml
    <property name="logLevel" value="info" /><!--日志的默认级别-->
```

然后重新编译一下（Eclipse 下选择Project -> Clean），再次运行，看看deub.log中是不是没有新的内容了。

日志还有一种重要的应用是用于记录程序中出现各种异常和错误，logback支持打印异常堆栈，以方便定位异常位置，下面代码演示了如何使用打印异常堆栈：

```java
    @Test
    public void testExcpetion() throws Exception {
        try {
            int m = 0;
            int n = 5/m;
        } catch(Exception e) {
            logger.error("here error",e);
        }
    }
```

运行这个测试方法后，可以在/your/log/path/error.log中看到类似如下异常信息：

```
07-04 19:45:59 [main] ERROR [com.mafengwo.demo.SpringBootLogbackTest.testExcpetion 31] - here occurs
java.lang.ArithmeticException: / by zero
	at com.mafengwo.demo.SpringBootLogbackTest.testExcpetion(SpringBootLogbackTest.java:29)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.springframework.test.context.junit4.statements.RunBeforeTestExecutionCallbacks.evaluate(RunBeforeTestExecutionCallbacks.java:74)
	at org.springframework.test.context.junit4.statements.RunAfterTestExecutionCallbacks.evaluate(RunAfterTestExecutionCallbacks.java:84)
	at org.springframework.test.context.junit4.statements.RunBeforeTestMethodCallbacks.evaluate(RunBeforeTestMethodCallbacks.java:75)
	at org.springframework.test.context.junit4.statements.RunAfterTestMethodCallbacks.evaluate(RunAfterTestMethodCallbacks.java:86)
	at org.springframework.test.context.junit4.statements.SpringRepeat.evaluate(SpringRepeat.java:84)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:251)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:97)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.springframework.test.context.junit4.statements.RunBeforeTestClassCallbacks.evaluate(RunBeforeTestClassCallbacks.java:61)
	at org.springframework.test.context.junit4.statements.RunAfterTestClassCallbacks.evaluate(RunAfterTestClassCallbacks.java:70)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.run(SpringJUnit4ClassRunner.java:190)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:89)
	at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:41)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:541)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:763)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:463)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:209)
```

 从打印的异常信息我们可以清楚的看到是哪里出现的问题以及运行过程的方法调用栈信息。

