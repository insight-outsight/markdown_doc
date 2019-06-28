<script type="text/javascript" src="html_resources/jquery-3.3.1.min.js"></script>
<script type="text/javascript" src="html_resources/2html.js"></script>
<link rel="stylesheet" type="text/css" href="html_resources/style.css">

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

Java后台项目开发中不可避免地会需要引入其它软件包（jar包)，如果没有自动化管理工具，需要手动获取到目标jar文件，并把它拷贝到指定的引用路径中，才能被正常引用，这当然是重复性地繁琐工作。使用maven后，只需要在POM文件使用<dependency>标签声名所要引入jar包坐标（<groupId>、<artifactId>、<version>），就能唯一定位这个包，如果这个在本地不存在 ，将由maven自动下载到本地。至于<groupId>、<artifactId>、<version>是每个maven项目都必须具有的唯一标识，在上面的pom文件中，也定义了自身的坐标。

### 1.2 构建管理

maven在项目构建管理方面做得很完善，从maven的视角来看，一个项目的生命周期（lifecycle）可以分别编译、测试、打包、安装、部署五个阶段，这些阶段分别使用compile、test、package、install、deploy命令来执行，它们之间是顺序依赖的关系，即后面的命令执行时会自动执行它之前的所有命令，只有前面的命令执行成功才会接着后续执行。

![avatar](https://s2.ax1x.com/2019/06/26/ZexHHJ.md.png)

图1 Maven项目生命周期

compile：编译工程主体功能src/main/目录下代码并将结果输出到target目录。

test：执行src/test/java目录中的测试用例，依赖于主体功能代码编译通过以后才能进行。此步可使用构建参数跳过。

package：将编译后结果按格式（默认为jar）打包至target目录。

install：将package步骤中生成的jar包安装到本地资源库中。

deploy：将package步骤中生成的jar包部署到maven远程资源库。



此外，利用maven提供的丰富插件（包括很多第三方插件），能够使用简单的命令就可以完成很多复杂的任务，这里就不一一赘述。



## 2. 开发集成框架 - Spring

Spring可以说是Java生态第一开发框架了，它经历了近20年的发展，已经形成了涵盖系统集成、控制反转（IoC，也叫依赖注入）容器、AOP管理、Web后台开发、应用启动管理、微服务管理等领域的技术生态，它为企业级应用开发提供了完整解决方案。限于篇幅，本文只介绍Spring IoC容器、Spring Boot和Spring MVC框架三部分。

### 2.1 Spring IoC 容器

IoC容器是一种实现了对象自动创建并建立相互依赖关系容器，因此它又可以称作依赖注入。IoC容器负责根据配置或者注解定位、解析、实例化应用程序中的用到的对象并建立这些对象间的依赖关系，这样应用程序就无需直接在代码中创建相关的对象，只需直接使用即可。



### 2.2 Spring Boot



### 2.3 Spring MVC

Spring MVC框架是Web MVC设计模式的Java版本的实现，它提供一种基于HTTP协议请求/响应交互模型的轻量级Web开发架构，它实现了模型（M）、视图（V）、控制器（C）层的职责分离和系统解耦，与Spring框架无耦合，并能无缝集成到Servlet容器中。这种基于请求驱动类型的MVC框架把一些Web通用处理逻辑（如参数接收、验证、结果返回等）在框架层面进行了抽象封装，使得开发者无须对这些Web层的固化流程进行重复性编码，只需遵照它的规则去开发特定业务逻辑，从而简化了Web层的开发。




## 3. ORM框架 - Batis

### 3.1 配置文件方式

### 3.2 配置文件方式

## 4. 缓存访问


##5. 序列化


##6. 字符串处理


##7. 编解码


##8. 日志组件


##9. 单元测试框架
