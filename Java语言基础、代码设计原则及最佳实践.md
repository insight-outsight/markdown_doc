#Java语言基础、代码设计原则及最佳实践

##1. Java语言基础
Java是一种基于Java虚拟机（JVM）、跨平台、面向对象的编程语言。

### 1.1 Java程序开发流程

![avatar](https://s2.ax1x.com/2019/06/19/VO3VX9.png)
图1  Java程序开发流程

描述开发流程，分3阶段，HelloWorld,import,maven dependency



### 1.2 Java虚拟机

Java虚拟机（以下简称为JVM）是一个虚拟的计算机设备，它屏蔽了不同操作系统的差异，使同样的代码（字节码）在不同的操作系统上都能运行，这只需要在不同类型的操作系统上安装相应的JVM。

#### 1.2.1 支持多种编程语言
Java语言的程序编译成字节码后就可以运行在JVM上，但JVM本身却不并只可以运行Java语言的程序编写的程序，一切能编译成标准Java字节码的程序语言的代码都可以在JVM上运行，例如Scala、Kotlin等，JVM只面向字节码，与语言没有关联。

![avatar](https://s2.ax1x.com/2019/06/20/VvuzLR.png)

图2 Java平台多编程语言支持示意图

上图2展示Java和Scala语言是如何通过一样的开发流程运行在JVM上的。所有能在JVM平台运行的语言都可以称为JVM平台语言。JVM平台语言代码库之间可以相互调用。

#### 1.2.2 运行时架构


![avatar](https://s2.ax1x.com/2019/06/19/VO3Kk6.png)

图3 JVM运行时架构

#### 1.2.3 内存模型

Java内存模型（JMM）规定了所有的变量都存储在主内存中，每条线程还有自己的工作内存（又叫本地内存），线程的工作内存中保存了该线程中是用到的变量的主内存副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存。不同的线程之间也无法直接访问对方工作内存中的变量，线程间变量的传递均需要自己的工作内存和主存之间进行数据同步进行。
而JMM就作用于工作内存和主存之间数据同步过程。他规定了如何做数据同步以及什么时候做数据同步。工作内存是JMM的一个抽象概念，它对应的具体存储空间（存储介质、地址）是不确定的，涉及到CPU缓存，写缓冲区，寄存器以及其他的硬件和编译器优化等方面。
![avatar](https://s2.ax1x.com/2019/06/20/VvlBff.md.png)
图4 JVM内存模型

###1.3 Java语言平台体系结构
![avatar](https://s2.ax1x.com/2019/06/19/VO3pT0.png)
图5 Java平台体系结构






###1.4 Java规范及实现

Java语言是一个由标准化发展而来，并且朝着标准化发展而去的语言，这种标准化通过JSR来管理，JSR由JCP负责实施执行。
<br>
下面解释下这两个概念：

JCP：Java Community Process，是一个开放性的 Java技术规范管理组织 ，成立于1998年，[官网](https://jcp.org)，由社会各界Java团体或者个人组成的社区，其职责是规划和领导Java的发展，不断更新Java技术规范，其成员列表[在这里](https://jcp.org/en/participation/members)。其中，由24个成员组成的执行委员会则是该组织的最高决策机构，负责规划Java的技术演进方向。



JSR：Java Specification Requests，Java规范请求草案，是由JCP成员向JCP执行委员会提交的Java发展议案，经过一系列流程后，最终通过的JSR会体现在未来的Java平台中。



以下列举一些JSR规范：

| 编号 | 内容 | 备注 |
| ---- | ---- | ---- |
| JSR 14  | Add Generic Types To The Java Programming Language | 泛型支持 |
| JSR 365 | Contexts and Dependency Injection (CDI) 2.0        | 依赖注入管理 |
| JSR 369 | Java Servlet 4.0                                   |      |
| JSR 370 | Java API for RESTful Web Services (JAX-RS) 2.1     |      |
| JSR 367 | The Java API for JSON Binding (JSON-B) 1.0         |      |
| JSR 374 | Java API for JSON Processing (JSON-P)1.1           |      |
| JSR 380 | Bean Validation 2.0                                |      |
| JSR 250 | Common Annotations 1.3                             | 通过注解规范 |
| JSR 338 | Java Persistence 2.2                               | 持久化规范 |
| JSR 356 | Java API for WebSocket 1.1                         |      |
| JSR 919 | JavaMail 1.6                                       |      |
| JSR 107 | JCACHE - Java Temporary Caching API                | 内存缓存规范 |


表1 一些JSR规范列表

<br>

JSR定义的只是规范，具体实现可以由JVM平台本身或者第三方来完成。

<br>
每一个Java语言主要版本的发布，都会有相应的规范文档随之公布，其中主要包含语言规范和虚拟机规范。例如最新的JDK 12的规范文档为：

[The Java Virtual Machine Specification, Java SE 12 Edition](https://docs.oracle.com/javase/specs/jvms/se12/jvms12.pdf)

[The Java Language Specification, Java SE 12 Edition](https://docs.oracle.com/javase/specs/jls/se12/jls12.pdf)

这是学习Java语言最好的资料。


###1.5 Java编程指南
####1.5.1 包

Java中的包有两种，一种是package包，一种是jar(Java Archives)包。

##### package

package是Java中组织代码结构的基本单位，它是带有可见性语义的细粒度的类的集合，默认情况下一个类只能被它所在package包内的类访问到。所以一般将一些功能上相关或者结构上相似的类放进同一package中。

同时package也给类引入了命名空间，每个类都属于唯一一个确定的package。不同package里允许有同名的类。

##### jar

jar包由一个或者多个package经过的归档压缩形成的包，以便发布、依赖管理。

####1.5.2 接口与类
作为一门经典的面向对象编程（Object Oriented Programming, OOP）语言，Java严格恪守OOP的信条 - 封装、继承、多态。
接口（interface）和类（class）作为OOP的载体在实际编程中扮演着重要角色。可以说，在Java程序中一切皆是类(接口)。封装、继承、多态这些特性都是通过类（接口）机制来实现。

######什么是接口？
接口是对某一类对象特征与行为的抽象，它定义了某一类对象一个或多个方法。接口和它的方法都是抽象的，没有实际的代码逻辑，因而也不能用它创建对象。

```java
public interface IHello {
    void sayHello();
}
```


######什么是类?
类是对某一类对象属性与行为的抽象与实现，它定义并实现了方法。它可以是单独存在，也可以在其中实现某些已知的接口的方法。类可以用来创建对象，这也是类的最大用途。

```java
public class TextHelloImpl implements IHello{

    @Override
    public void sayHello() {
        System.out.println("say hello.");  
    }

}
```



######还有抽象类？
抽象类（abstract class）是含有抽象方法的类，一般作为接口与其实现类之间的桥梁的存在。抽象类无法用来创建对象。

```java
public abstract class AbstractHello implements IHello{

    private String helloText = "你好";
    
}
```



#####1.5.2.1 类访问权限作用域

Java中类(属性、方法)访问权限作用域修饰符有四种，分别是public、protected、private和defaut(默认的访问权限是包)。除了默认的包作用域外，分别使用**public、protected、private**关键字表示。不同的修饰符拥有的访问权限作用域如下表所示。

| 修饰符    | 本类 | 本包中的类 | 子类 | 其它任何类 |
| --------- | ---- | ---------- | ---- | ---------- |
| public    | √    | √          | √    | √          |
| protected | √    | √          | √    | ㄨ         |
| default   | √    | √          | ㄨ   | ㄨ         |
| private   | √    | ㄨ         | ㄨ   | ㄨ         |

√：代表有权限访问

ㄨ：代表无访问权限

表2 不同修饰符代码访问权限列表

#####1.5.2.2 类构造器

类构造器是在创建类对象的时调用的一个方法，用来完成对象初始化工作，Java中类构造器是一个特殊的Java方法，它的方法名必须与类名完全相同，它由访问权限、类名、构造参数（可选）组成。不含有构造参数的构造器称为无参构造器。下面代码是一个例子：

```java
public class Cat {

    private String name;
    private String color;
    private int age;
    
    public Cat() {
    }

    public Cat(String name, String color, int age) {
        this.name = name;
        this.color = color;
        this.age = age;
    }
    
}
```

一个类可以不定义构造器，这时编译器将会为它自动生成一个无参构造器，这个构造器的方法体为空。

#####1.5.2.3 类的静态方法

Java中支持类的静态方法，即不需要创建类对象，就能调用的方法。下面代码示例定义了包括静态方法的类：

```java
public class Utils {

    public static String joinStringWithUnderline(String str1,String str2) {
        return str1+"_"+str2;
    }
    
}
```

然后，其它类可以这样调用，

```java
public class CallStaticMethodDemo {
   
    public static void main(String[] args) {
        String joinedString = Utils.joinStringWithUnderline("abc","123");
        System.out.println(joinedString);//打印的内容为abc_123
    }
    
}
```



#####1.5.2.4 类的继承

Java中类的继承只允许单一继承，即每个类有且只有一个父类，要么显式指明父类，要么自动继承一个默认父类——Object类，Object类定义了所有类对象都应该具备的最基本的一些方法，例如toString(),hashCode()。
Java中类的继承是可传递的，即一个继承的父类的属性和方法可以传递给继承它的子类。

#####1.5.2.5 类的多态

Java中类的多态本质上是方法代码的运行时绑定，它是通过面向接口编程实现的。下面用一个实际例子来演示多态性。

```java
public class AudioHelloImpl implements IHello {

    @Override
    public void sayHello() {
        System.out.println("say hello by audio.");  
    }

}
```

```java
public class PolymorphismDemo {

    public static void main(String[] args) {

        IHello textHello = new TextHelloImpl();
        IHello audioHello = new AudioHelloImpl();
        hello(textHello);//打印say hello by text.
        hello(audioHello);//打印say hello by audio.
        
    }
    
    public static void hello(IHello hello) {
        hello.sayHello();
    }

}
```

以上代码的执行结果是打印以下内容：

```
say hello by text.
say hello by audio.
```



####1.5.3 数据类型
Java是一种强类型语言，它规定的数据类型可分为两大类，一类是基本数据类型，一类是引用数据类型，它又可分为类对象类型、接口类型、数组类型等几种。
Java中有8种基本数据类型（primitive types）,每种基本类型都有对应的对象类型。关于基本类型与对象类型的主要区别在于：

  - 基本类型属于只能用于数值运算，它不属于对象。而对象类型不仅能用于数值运算，而且可以通过自身方法调用完成一些功能，例如，如果有Integer类型变量i，可以调用i.longValue()方法将其转换成long型变量。
  - 基本类型存储空间在栈内分配，而对象类型在堆内分配。
  - 在进行方法调用传参时，基本类型的参数会进行值传递，而对象类型的是进行地址传递。
<br>

| **基本类型（对象类型）** | **字节** | **默认值** | **取值范围** | **示例** |
| ------------------------ | ---- | ---------- | ------------ | ------------------ |
| byte(Byte) | 1 | 0 | -2^7-2^7-1 | byte b=10; |
| char(Character) | 2 | ' \u0000' | 0-2^16-1 | char c='c'; |
| short(Short) | 2 | 0 | -2^15-2^15-1 | short s=10; |
| int(Integer) | 4 | 0 | -2^31-2^31-1 | int i=10; |
| long(Long) | 8 | 0 | -2^63-2^63-1 | long n=10L; |
| float(Float) | 4 | 0.0f | -2^31-2^31-1 | float f=10.0F |
| double(Double) | 8 | 0.0d | -2^63-2^63-1 | double d=10.0; |
| boolean(Boolean) | 4/1 | false | true/false | boolean flag=true; |
表2 Java基本数据类型
<br>
说明：

- 在声明定义基本类型字面量变量时，如果不明确指定，整数型的默认是int，带小数的默认是double。例如：int i = 3;double d = 3.14;
- boolean类型单独使用是占4个字节，在数组中是1个字节。
- 如果需要在计算中不允许任何的误差，应该使用BigDecimal类，而不是float或者double。
- 从Java5开始，基本类型与其及对应对象类型之间可以实现自动包装/解包（Wrapper/UnWrapper)，即实现在编程中通用。
- 从Java7开始，可以加前缀0b来代表二进制数，例如：byte b = 0b1000;表示b的数值是8。数字字面量可以加上下划线提高易读性，例如：int i = 200_0000;代表i的数值是200万。
- 多线程应用场景下，为保证long和double类型变量读写都是原子操作，应使用volatile关键字修饰变量。

####1.5.4 程序流控制

Java中的程序控制语法与C/C++等语言基本一致，主要有包括 **if-else，for，continue，break，return，while，do-while**等关键字。下面逐一介绍：

####1.5.4.1 if-else

```java
    public static void main(String[] args) {

        int a = 3;
        int b = 4;
        
        if (a > b) {
            System.out.println("a 大于 b");
        }
        else {
            System.out.println("a 不大于 b");
        }
        
        if (a > b) {
            System.out.println("a 大于 b");
        }
        else if (a < b ){
            System.out.println("a 小于 b");
        }
        else {
            System.out.println("a 等于 b");
        }
        
    }
```

以上代码的执行结果是打印以下内容：

```
a 不大于 b
a 小于 b
```
####1.5.4.2 for、continue、break、return

for用作循环，它有两种形式，一种形式是：

```java
for (int i = 0; i < 3; i++) {
  System.out.println(i);
}
```

以上代码的执行结果是打印以下内容：

```
0
1
2
```

另一种形式是：

```
List<String> flowerNameList = Arrays.asList("rose","chrysanthemum");
for (String flowerName : flowerNameList) {
  System.out.println(flowerName);
}
```

以上代码的执行结果是打印以下内容：

```
rose
chrysanthemum
```



continue、break、return这几个关键字用于精确流程控制，可用于for, while, do-while循环代码块中，continue表示当满足某种条件时，结束本次循环，继续执行下次循环。break表示当满足某种条件时，结束本次循环，并跳出循环代码块，不再执行本循环块中剩余的循环。return则表示结束本次循环并终止本方法中剩余代码的执行，返回方法的调用处位置。以下分别使用代码演示：

```java
        for (int i = 0; i < 3; i++) {
            if (i == 1) {
              continue;  
            }
            System.out.println(i);
        }
        System.out.println("continue test finish");
```

以上代码的执行结果是打印以下内容：

```
0
2
continue test finish
```


```java
        for (int i = 0; i < 3; i++) {
            if (i == 1) {
              break;  
            }
            System.out.println(i);
        }
        System.out.println("break test finish");
```

以上代码的执行结果是打印以下内容：

```
0
break test finish
```



```java
        for (int i = 0; i < 3; i++) {
            if (i == 1) {
              return;  
            }
            System.out.println(i);
        }
        System.out.println("return test finish");
```

以上代码的执行结果是打印以下内容：

```
0
```

####1.5.4.3 while、do-while

whille、do-while循环与for循环类似，只不过它只需要一个循环条件，例如

```java
        int i = 0;
        while (i < 3) {
            System.out.println(i);
            i++;
        }
```

以上代码的执行结果是打印以下内容：

```
0
1
2
```



```java
        int i = 0;
        do {
            System.out.println(i);
            i++;
        } while (i < 3);
```

以上代码的执行结果是打印以下内容：

```
0
1
2
```



####1.5.5 异常

####1.5.6 泛型

####1.5.7 多线程


##2. 代码设计原则
###2.1 极简主义原则

代码极简主义的含义是在能达到预期结果的前提下使代码保持最简洁明了。

这样做的成本是：会多花费一些时间构思、对比各种实现方案。
这样做的收益是：能保持代码量最小，便于维护，避免复杂性过高带来的潜在代码漏洞以及维护困难。

###2.2 不重复发明轮子原则
在做程序开发之前，应先多了解团队内部、开源社区等是否已有代码资源可以用于项目开发中，如果已有，则应首先考虑使用它们。

这样做的成本是：需要预先沟通、学习来了了解⼀一些已有类库API。 
这样做的收益是：可以避免重复性劳动、提⾼开发效率。 

###2.3 开闭原则

开闭原则是指软件实体如类、方法等应当对扩展开放，对修改关闭，其含义是一个软件实体应该通过扩展已有代码来实现需求变化，而不是通过修改已有的代码来实现。扩展的方式可以是继承、聚合等。

开闭原则是OOP最基础的设计原则。

这样做的成本是：编码之前需要对待解决问题有比较全面、深入的分析，以便抽象出代码层次结构，明确开与闭的部分。
这样做的收益是：可以提高代码复用性，避免重复性开发，减少由于直接修改原代码引发错误的风险。可以提高代码可维护性，使后续的代码维护人员无须通读已有代码即可实现代码扩展。

###2.4 依赖倒转原则

实际编程中，都会存在高层模块调用低层模块代码的情况，通常的做法是高层代码通过直接创建低层代码对象来完成，

但这样做造成了两者之间的强绑定关系。依赖倒转原则要求低层代码尽量都要有抽象类或者接口（或者两者都 有），高层代码则只依赖低层代码的抽象接口，例如List list = new ArrayList();

这样做的成本是：要为每个暴露给外部使用的功能主体提供抽象接口。
这样做的收益是：高层对低层实现的变化是无感知的，实现了功能解耦。



##3. 最佳实践
###3.1 合理地使用包组织代码结构

Java工程代码应该使用按照设计层次划分成不同的包（package），这样不仅能体现出工程结构层次清晰，还能充分利用包作用域对代码进行安全防护。

###3.2 恰当地使用类、方法作用域

类、方法的作用域是用来作访问控制的，这意味着当程序员编写一个Java类时，可以选择哪些其它类拥有该类、方法的使用权，这种访问控制在大型多人协作的工程中尤为重要。

一个类或者方法是内部使用还是对外提供服务，对类作者的职责要求是完全不一样。其中一个最直接的问题是，当一个对外提供服务的类在后续维护、更新时都要考虑上游使用者兼容性与升级问题。

###3.3 识别并使用代码设计模式

设计模式是一套被反复广泛使用过代码设计经验和技巧的总结，它的价值在于使用这些设计模式编写的代码是可重用性强、高效率、易于理解、便于扩展的。因此，当进行代码设计时如果能使用合适的设计模式，将会提升代码品质，但这种能力需要长期学习和积累，所以最好从开始阶段就做起。

###3.4 融入开源世界

Java生态有大量优秀的开源软件包，其中很多都是经历了多年不断地技术演进，已趋于成熟，使用它们进行项目开发可以减少重复的编码，提高生产效率。由于这些软件都是开源的，开发者很容易对其实现原理和源码进行研究，并可以尝试优化和扩展，有想法或者疑问还可以与开源社区互动，这是Java世界的一个魅力之处。

所以，学习、使用、分享，去融入Java开源世界。

###3.5 使用相对通用的软件包版本

不管是JDK还是各种开源软件包，版本兼容性问题是一个长期困扰开发者的难题，在同一项目引入多个包，可能引发相互之间版本不兼容，最终导致项目无法按期进行。所以在使用开源软件包时，一定要确认相互之间的兼容性，还有就是不要随意升级某一个软件包版本。

###3.6 代码评审常态化

代码评审是保证代码质量的重要手段，应将其作为软件开发的一个重要环节，全面落实。实际执行中，最好制定出代码评审规范，使之标准化，流程化。







