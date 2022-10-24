# Java 工程师修炼之道

读《Java 工程师修炼之道》整理笔记

对于后端技术，一般的关注点为以下几个指标：

* **可用率**：能够提供正常服务的时间占线上运行时间的百分比。
* 稳定性：服务在异常和危险情况下保持稳定的能力。
* 容错性：在服务出现错误或异常时，能够继续提供一定的服务
* 扩展性：通过扩展（而非修改）现有系统的能力来满足需求。
* 可维护性
* 安全性

## 后端基础设施

后端基础设施主要指的是：应用软件在线上稳定运行需要依赖的关键组件或者服务。

使用Java 后端技术的目的就是 构建业务应用，为用户提供在线或者离线服务。



![后端基础设施](D:\MarkDown 笔记\Java 工程师修炼之道\后端基础设施.png)



### 请求统一入口 ——API 网关

在开发过程中，通常后端提供的接口需要以下功能的支持：

* 负载均衡
* API 访问权限控制
* 用户鉴权

API 网关将这几种功能集成到一起作为一个服务，这样既可以动态地修改权限控制和鉴权机制，也可以减少每个业务集成这些机制的成本。

### 业务应用和后端基础框架

业务应用 分为：在线业务应用 和 内部业务应用。

其中 **在线业务应用**：直接面对互联网用户的应用，接口等。

**内部业务应用**：主要面对公司内部用户的应用。

<font color=red>业务应用是基于 后端基础框架进行开发的。</font>

* **MVC 框架**：统一开发流程，提高开发效率，屏蔽一个关键细节的 Web/后端框架。如：Spring MVC，Jersey，阿里的 Webx。
* **IOC 框架**：可以实现依赖注入/控制反转的框架。如： Spring
* **ORM 框架**：能够屏蔽底层数据库细节，提供统一的数据访问接口的数据库操作框架。MyBatis
* **缓存框架**：对 Redis , Memcached 这些缓存软件操作的统一封装，能够支持客户端分布式方案，主从等。如 Spring 的 RedisTemplate , Jedis。
* **Java EE 应用性能框架**：对于线上的 JavaEE 应用，需要有一个统一的框架集成到每一个业务中检测每一个请求，方法调用，JDBC 连接，Redis 连接等的耗时，状态等。Jwebap 是一个可以使用的性能检查工具（长时间未更新）。



### 缓存，数据库，搜索引擎，消息队列

**后端基础服务**：缓存，数据库，搜索引擎，消息队列。

* **缓存**：用来解决热点数据的访问问题，是提高数据查询性能的强大武器。
* **数据库**：绝大数业务数据都是持久化存储在数据库中的。
* **搜索引擎**：针对全文检索以及数据各种维度查询设计的软件。
* **消息队列**：数据传输的一种方式就是通过消息队列。

### 文件存储

**不管是业务应用，依赖的后端服务还是其他的各种服务，最终都要依赖于底层文件存储**。

通常来说，文件存储需要满足的特性有：**可靠性，容灾性，稳定性**，既要保证存储的数据不轻易丢失，即使发生故障也能够有回滚方案，也要保证高可用。**目前 Hadoop 的 HDFS 则是普遍的分布式文件存储方案。**

### 统一认证中心

统一认证中心：为了能够集中对所有 APP 都会用到的信息进行管理，也给所有应用提供统一的认证服务。

* 用户的注册，登录验证，Token 鉴权
* 内部消息系统用户的管理和登录鉴权。
* APP 的管理，包括APP 的 secret 生成，APP 信息的验证（如验证接口签名）等。

### 单点登录系统

通过统一认证中心构建移动APP 的单点登录：模仿Web的机制，将认证后的信息加密存储到本地存储中，供多个APP 使用。

通俗地来说：只需要一次用户登录，就能够进入多个业务应用（权限可以不相同），方便用户操作。

![image-20200512113208601](D:\MarkDown 笔记\Java 工程师修炼之道\Java 工程师修炼之道.assets\image-20200512113208601.png)



### 统一配置中心

统一配置中心：统一对所有业务或者基础后端服务的相关配置文件进行管理。

在 Java 后端中，一种读写配置比较通用的方式就是将配置文件写到 Propetis, YAML ,HCON 等文件中，修改的时候只需要更新文件重新部署，可以做到不牵扯代码层。

* 能够在线动态修改配置文件并生效
* 配置文件可以区分环境（开发、测试、生产等）
* 在Java中可以通过注解，XML 配置的方式引入相关配置。

百度开源的 Disconf 是一个可以在生产环境中使用的方案，也可以根据自己的需求开发自己的配置中心。

### 服务治理框架

对于外部API 调用或者客户端对后端API 的访问，可以使用 HTTP 协议或者 RESTful。

对于内部服务间的调用，一般都是通过RPC 机制来进行的，目前主流的 RPC 协议有：RMI，Hessian，Thrift，Dubbo。

传统的 ESB（企业服务总线）本质上就是一个服务治理方案，但ESB作为一种 Proxy 的角色存在于 Client 和 Server 之间，所有的请求需要经过ESB,这样使得 ESB 很容易成为性能瓶颈。

以配置中心为枢纽，调用关系只存在于 Client 和提供服务的 Server 之间，这就避免了传统的 ESB 的性能瓶颈问题，对于这种设计 ESB 应该支持的特性如下：

* 服务提供方的注册，管理
* 服务消费方的注册，管理
* 服务的版本管理，负载均衡，流量控制，服务降级，资源隔离
* 服务的容错，熔断



### 统一调度中心

统一调度中心：对所有的调度任务进行管理，这样能够统一对调度集群进行调优，扩展，任务管理等。

定时调度：针对各自的任务，依赖Linux 的 Cron 机制或者 Java 中的 Quartz 来实现调度，比如定时去抓数据，定时刷新订单。

* 根据 Cron 表达式调度任务
* 动态修改，停止，删除任务
* 支持任务分片执行
* 支持任务工作流，比如一个任务完成之后再执行下一个任务
* 任务支持脚本，代码，URL 等多种形式
* 任务执行的日志记录，故障报警



### 统一日志服务

统一日志服务：使用单独的日志服务器记录日志，各个业务通过统一的日志框架将日志输出到日志服务器上。

可以通过实现 Log4j 或者 LogBack 的 appender 来实现统一日志框架，然后通过 RPC 调用将日志打印到日志服务器上。



## Java 项目与工程化

工程化：采用各种机器或者人工手段解决诸如 如何协作，如何规范化，如何保证质量，如何计划，如何控制进度等问题，以达到减少繁琐的重复性劳动，使得流程高效并且可重复。

具体到 Java 项目中，可以从 以下几方面看待工程化需要解决的问题：

* 项目构建
* 代码版本控制
* 代码质量保证



### 项目构建

对于 Java 项目来说，第三方类库依赖，构建流程化，项目结构标准化等都是 项目构建构建工具解决的问题。

项目构建指的是：完成工程发布流程需要的一系列步骤，包括编译，测试，打包，部署等。

#### 传统构建工具—— Ant

####主流构建工具—— Maven

Maven 是继 Ant 之后出现的一款 **基于约定优于配置原则**的项目构建工具。

约定优于配置： 约定的一些规范无须再配置，例如其约定好的生命周期，项目结构等。

功能：

* 依赖管理
* 规范目录结构
* 完整的项目构建阶段
* 支持多种插件
* 方便集成
* 可以通过每次发布都更新版本号以及统一配置文件来规范软件包的管理。



Maven 基于 POM 进行。一个项目的所有配置都被放置在 pom.xml 中，包括定义项目的类型，名字，管理依赖关系，定制插件的行为等。

* Maven 使用 groupId:artifactId:version 三者来标识一个唯一的二进制版本，可以缩写为 GAV
* packaging 代表打包方式，可选 pom , jar（默认）, war, ear,custom
* properties 是全局属性的配置
* dependencies 是对于依赖的管理
* plugins 是对于插件的管理
* maven 可以通过 parent 实现 POM 的继承以完成统一配置管理，子 POM 中 配置优先级高于 父 POM.

#### 新兴构建工具——Gradle

基本上现在所有的 Android 项目都采用 Gradle 作为项目构建工具。



### 代码版本控制

目前常用的版本控制系统（VCS） 包括 SVN 和 Git

#### 集中式代码版本管理—— SVN

SVN 是几年前用到最普遍的一个 VCS 工具，采用了分支管理系统，**集中式版本控制**，依赖于中央版本服务器。

#### 分布式代码版本管理——Git

Git 是一个分布式版本控制系统，相比 SVN 的集中式版本控制，每个工作计算机都可以作为版本库，可以不依赖远程仓库服务器离线工作。

##### 流程与常用命令

* git clone [repository url] ：复制项目的 Git 地址
* git checkout [branchName]: 切换项目分支
* git add [fillName]：增加或改动了一些文件
* git commit -m <message> 提交到本地仓库
* git push origin master 提交到远程仓库
* git pull  从远程库更新代码并合并（是 git fetch  和 git  merge 的合并）

##### 服务器搭建

Git 是发布式的，并不需要中央版本服务器即可使用，但是当多人协作时，中央版本服务器必不可少。

目前比较知名，用到较多的 GitHub 实现，有以下几个：

* GitLab
* GitBucket

##### 工作流

* GitHub Workflow
* GitFlow

GitFlow 中定义了两大类分支：

* 主分支：主分支是所有开发活动的核心分支。所有的开发活动产生的输出物最终都会反映到主分支的代码中。
  * master 分支：存放的是随时可供在生产环境中部署的代码。每一次更新，都添加对应的版本号标签（tag）.
  * develop 分支: 保存当前最新开发成果的分支。
* 辅助分支：用于组织解决特定问题的各种软件开发活动的分支。
  * feature 分支：用于开发新功能时所使用的分支
  * release 分支：用于辅助版本发布的分支
  * hotfix 分支：用于修正生产代码中有缺陷的分支



##### 提交日志的规范

撰写清晰规范的格式化日志有助于版本修改，查看历史记录等。SVN 的默认配置是允许提交空日志的，但Git 不允许日志为空。现在，**市面上有很多类似的提交日志规范，这里推荐使用 angular 规范**。

angular 规范的 commit message 包括 3 个部分 header , body 和 footer ，格式如下：

```
<type> (<scope>):<subject>
//空一行
<body>
//空一行
<footer>
```

* type :   用于说明 commit  的类别，只允许使用下面 7 个标识。
  * feat：新功能
  * fix：修补 Bug
  * docs：文档（document）
  * style：格式（不影响代码运行的变动）
  * refactor：重构
  * test：增加测试
  * chore：构建过程或辅助工具的变动
* scope ：用于说明 commit 影响的范围，比如 数据层，控制层，视图层等。
* subject ：是 commit 目的的简短描述
* body ：对本次commit 的详细描述



### 代码质量保证

代码质量分成以下几个等级：可编译，可运行，可测试，可读，可维护，可重用。

可测试性：保证你的代码能够经受住各种黑盒和白盒测试而不出现Bug.

#### 使用单元测试保证代码质量

单元测试，即Unit Test ，指的是对代码的各个接口的测试。所谓的“测试驱动开发”也是依赖于此。

####开发规范与建议

对于规范，可以使用 CheckStyle , PMD，FindBugs 等工具进行保证，使用相关的Maven插件即可。

开发建议：

* 务必撰写文档。
* 在代码无法阐述设计意图时，注释是必须的。
* 防御性编程：处理异常，不要相信外部输入，不要相信外部依赖。



## 开发框架

在一个可用的 Java Web 应用中，一般由以下几种框架构成：

* Ioc 框架：依赖注入/控制反转，即将依赖从代码层转移到了容器配置层面。Java 使用最普遍的框架
* ORM 框架：对象关系映射，即将数据库的表映射到Java中对象的一种数据库操作。
* Log 框架：日志框架，即记录应用运行，异常日志的框架。
* Web 框架：一般指的是 Model-View-Controller 的分层Web开发框架，将业务代码做了逻辑分层。

除了上述 4 种框架，还有安全框架，AOP 框架，缓存操作框架也是关键的开发框架。



### 依赖注入

IOC（依赖注入）：将代码里对象之间的依赖关系转移到容器中，这样就能够很灵活地通过面向接口的编程方式改变真正的实现类。

IOC 的作用就是将依赖关系的维护从代码中拿出来，通过容器来维护。

```
IoCContainer container = new IOCContainer("...")// 可以通过一个上下文配置文件，也可以通过扫描注解

IoCTest iocTest = container.getInstance("iocTest");
```

这样在想要改变实现类时，只需要修改配置文件。

目前常见的 Ioc 框架有如下几个：

* Google Guice
* PicoContainer
* Dagger
* Spring Framework

基本上所有的 Ioc 框架都支持构造器注入，setter 注入以及 字段注入 3中方式。



#### JSR-330 依赖注入规范

对于依赖注入，JSR-330 做了一些规范，该规范主要是面向依赖注入使用的，而对注入器实现，配置并未做详细要求。

```xml
<dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
    <version>1</version>
</dependency>
```

主要提供了以下几个注解和类：

* @Inject：注解标识了 可注入的构造器，方法或字段，可以用于静态或实例成员。对于同一类的字段是不区分注入顺序的，同一个类的方法以同：

  ```java
  public class IocTest{
      private IUser user;
      
      @Inject
      public void setUser(IUser user){
          this.user=user;
      }
  }
  ```

* @Qualifier：元注解，用来构建自定义限定符，任何人都可以定义新的限定器注解。

* @Named：@Qualifier 的一个限定器注解，可以指定依赖的名称。

* Provider<T>： 接口 Provide 用于提供类型T的实例。

* @Scope : 注解 @Scope 是一个元注解，用于标识作用域注解。

* @Singleton： 基于 Scope 注解实现的一个作用域注解，表示注入器只实例化一次的类型。



##### Guice

Guice 是 Google 开源的轻量级 Ioc 框架，兼容 JSR -330 规范。其用 Module 来定义所有元件的实际类别。依赖定义部分可以使用 JSR -330 的注解。

##### PicoContainer

##### Dagger

Dagger 是 Google 开源的一个框架，支持 Android 和 Java 。

##### Spring Framework

Spring 的 IoC 应该是 Java 开发中最常用的功能之一。它的 XML 配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="wanger" class="com.luis.dto.Person"></bean>
    
    <bean id="zhangsan" class="com.luis.dto.Person" init-method="init" destroy-method="over"></bean>
    
    <bean id="lisi" class="com.luis.dto.Person" scope="prototype"></bean>
    
    <bean id="lazy" class="com.luis.dto.Person" lazy-init="true"/></beans>
    
</beans>
```



使用代码：

```java
ClassPathXMlApplicationContext context = new ClassPathXmlApplicationContext("application.xm");

IOCTest iocTest = context.getBean(IOCTest.class);

iocTest.test();
```



从 Spring 2.0 开始引入了对 注解的支持，并且逐步兼容了 JSR - 330 规范。

| Spring              | JSR-330           | 备注                                                         |
| ------------------- | ----------------- | ------------------------------------------------------------ |
| @Autowired          | @Inject           | @Inject 注解没有 required 属性                               |
| @Component          | @Named            | JSR-330 规范并没有提供复合的模型，只有一种方式来识别组件     |
| @Scope("singleton") | @Singleton        | JSR-330 默认的作用域类似于 Spring 的 prototype ,而 Spring 默认的作用域是 单例的。 |
| @Qualifier          | @Qualifier/@Named | javax.inject.Qualifier 仅仅是一个元注解，用来构建自定义限定符。 |



##### 循环依赖问题

Ioc 中常见的问题就是 循环依赖。

```java
class TestA{
    @Inject TestB b
}

class TestB{
    @Inject TestC c;
}

class TestC{
    @Inject TestA a;
}
```

以上几个框架，对于循环的处理：

* 通过替换为 Provider<T> ，并且在构造器或者方法中调用 Provider 的 get 方法来打破循环依赖
* 如果不依赖 Provider ，对于构造器中的循环依赖是无法解决的，会抛出异常。
* 对于方法或字段注入的情况，将其依赖的一边放置到单例作用域中（可以缓存）。



### 对象关系映射

ORM ，是一种为了解决面向对象与关系型数据库不匹配而出现的技术，使开发者能够用面向对象的方式使用关系型数据库。

Hibernate 现在主要用于在传统企业应用的开发上，互联网领域中由于流量大，并发高的缘故主要以 MyBatis 为主。

**一般的 ORM 包括以下几个部分**：

* 一个规定 Mapping Metadata 的工具，即数据库中的表，列与对象以及对象属性的映射。
* 一个对持久化对象进行 CRUD 操作的 API
* 一个语言或 API 用来规定与类和类属性相关的查询
* 一种技术可以让 ORM 的实现同事务对象一起进行 缓存，延迟加载等操作。



#### 表元数据的映射

`mybatis-config.xml` :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 设置日志输出为LOG4J -->
        <setting name="logImpl" value="LOG4J" />
        <!--将以下画线方式命名的数据库列映射到 Java 对象的驼峰式命名属性中-->
        <setting name= "mapUnderscoreToCamelCase" value="true" />
    </settings>
    <!--简化类命名空间 -->
    <typeAliases>
        <package name="tk.mybatis.simple.model" />
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="UNPOOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url"
                    value="jdbc:mysql://localhost:3306/mybatis?useSSL=false" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--常规做法-->
        <!--<mapper resource="tk/mybatis/simple/mapper/CountryMapper.xml" />-->
        <!--<mapper resource="tk/mybatis/simple/mapper/PrivilegeMapper.xml" />-->
        <!--<mapper resource="tk/mybatis/simple/mapper/RoleMapper.xml" />-->
        <!--<mapper resource="tk/mybatis/simple/mapper/RolePrivilegeMapper.xml" />-->
        <!--<mapper resource="tk/mybatis/simple/mapper/UserMapper.xml" />-->
        <!--<mapper resource="tk/mybatis/simple/mapper/UserRoleMapper.xml" />-->
        <!--第二种做法-->
        <package name="tk.mybatis.simple.mapper"/>
    </mappers>
</configuration>
```



`environment` 元素体中包含了 事务管理和连接池的配置，能够根据不同的环境使用不同的数据库配置。

`dataSource` 被设置为 POOLED 时使用了 MyBatis 自己提供的数据连接池。

`mappers` 元素则是包含一组 Mapper 映射器（这些 Mapper 的 XML 文件包含了 SQL 代码和映射定义信息）。

**`MyBatis` 支持注解映射 `Mapper`**



#### CRUD 以及属性的查询

MyBatis 的核心是 SqlSessionFactory ，要使用 它最基本的操作 API，首先获取 SqlSessionFactory，然后再获取相应的 Mapper。

```java
String resource = "classpath:mybatis-config.xml"
InputStream inputstream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

SqlSession session = SqlSessionFactory.openSession();
TestUserMapper mapper = session.getMapper(TestUserMapper.class);
TestUser testUser = mapper.selectUser(1);
```



在 Spring 中可以使用 MyBatis 提供的 org.mybatis.spring.SqlSessionTemplate 进行操作：

```java
SqlSessionTemplate sqlTemplate = new SqlSessionTemplate(sqlSessionFactory);
TestUser testUser = (TestUser) this.sqlSessionTemplate.selectOne("me.SelectUser",uid);
```



需要注意的是，**SqlSession 是非线程安全的，而 SqlSessionTemplate 则是线程安全的**。

`#{id}`  ： Mybatis  创建预处理语句属性并以它为背景设置安全的值，对应于 PreparedStatedment 中 的 ？/

`$` ：只是做 字符串替换，不会修改或转义字符串。因此，能使用 # 的地方就不要用 $ ，除非是像 order by 这种不是参数的地方。



#### 查询缓存的使用

MyBatis  支持一级缓存 和 二级缓存：

* MyBatis 默认开启 一级缓存， 其是 SqlSession 级别的，Session 结束缓存即清空。另外，还支持 Statement 级别，即 缓存 只对 当前的 Statement 有效（没什么用）。
* MyBatis 的二级缓存是 Mapper 级别的，被多个 SqlSession 共享，需要手动开启。



针对某个 **select 设置 useCache 为 false 来关闭二级缓存，设置 flushCache 为 true 使得数据直接flush 到数据库中防止出现脏读数据**。

注意：**MyBatis 中的缓存设计是针对单点应用的，都是 本地缓存，现在的分布式应用需要慎重开启**。只是单纯地把 MyBatis 作为一个 ORM 框架，在 Service 层自已实现缓存机制是更好的选择。



#### 结果的映射

 **MyBatis 默认会自动映射查询结果，根据 SQL 返回的列名并在 Java 类中查找相同名字的属性（忽略大小写）**，还可以全局配置将数据库列的 LowUnderscore (a_column) 命名转为 LowCannel 命名（aColumn）。



#### 规范 SQL 书写的语句构建器

MyBatis 提供了 SQL 语句构建类 org.apache.ibatis.jdbc.SQL，可以大大简化动态 SQL 编写问题。



#### 使用提示

* 当几个 SQL 语句 都包含同样的部分 SQL 逻辑时，可以使用 `<include refid = "id">` 来进行复用。

  ```xml
  <sql id="getTestByStatus_fragment">
      from test_meta where status = #{status}
  </sql>
  
  <select id='getTestList' parameterType ='map' resultMap = 'Test'>
      select id
      <include refid = "getTestByStatus_fragment"/>
      order by
      create_time desc
  </select>
  ```

* 动态SQL 中空字符串判断

  在动态 SQL 中 判断是否是空字符串时 ，MyBatis 的内建机制不太好用，建议使用以下方式：

  `<if test = "param != null and param != '' ">`

* 多 resultMap 复用

  一个应用中由于有不同功能经常会有多个 mapper.xml ，如果一个文件需要另一个文件中的 resultMap 的定义，可以直接引用，而不需要再重新定义一遍。

  ```xml
  <!-- A.mapper.xml-->
  <mapper namespace = "me.rowkey.pje.mybatis">
      <resultMap id="userResultMap" type="me.rowkey.pje.mybatis.UserDto"></resultMap>
  </mapper>
  
  <!-- B.mapper.xml-->
  <mapper namespace = "me.rowkey.pje.test">
      <select id ="bSql" parameterType ="map" resMap= "mmew.rowkey.pje.mybatis.userResultMap">
      </select>
  </mapper>
  ```

  

### 日志

java 中 成熟的 日志框架：

* JDK Logging
* Apache Log4j
* Apache Log4j2
* Logback



两个用于实现日志统一的框架 Apache Commons Logging 和 SLF4J 。与上述框架的不同之处在于：这两个框架只是一个门面，并没有日志框架的具体实现，可以认为是日志接口框架。



对于这些日志框架来说，一般会解决日志中的以下问题：

* 日志的级别
* 日志的输出目的地
* 日志的输出格式
* 日志的输出优化



#### JDK Logging

JDK Logging 就是 JDK 自带的日志操作类，在 Java.util.logging 包下面，通常被简称为 JUL。

```java
public class LoggerTest{
    private static Logger logger = Logger.getLogger(this.getClass().getName());
    
    public static void main(){
       logger.info("logger info");
    }
}
```

性能优化：

JDK Logging 是一个 比较简单的日志框架，并没有提供异步，缓存等优化手段，也不建议大家使用此框架。



#### Log4j

目前 Java 开发中使用最广泛的日志框架.



```java
import org.apache.log4j.Logger;
class Test{
    private static final Logger LOGGER = Logger.getLogger("this.getClass()");
    
    public void test(){
        LOGGER.info("...");
    }
}
```



Log4J 为了应对某一时间内大量的日志信息进入 appender 的问题，提供了缓存来进一步优化性能。



除了缓冲外，Log4J 还提供了 AsyncAppender 来做异步日志，但是 AsyncAppender 只能够通过 XML 配置使用。



#### Log4j2

两个部分：

* log4j-api：作为日志接口层，用于统一底层日志系统。
* log4j-core：作为上述日志接口的实现，是一个实际的日志框架。



```java
private static final Logger LOGGER = LogManager.getLogger(this.getClass());

LOGGER.debug("log4j debug message");

LOGGER.debug("error:{}",e.getMessage());
```

注意：Logger 是 log4j-api 中定义的接口，而 Log4J 中的 Logger 则是类。



#### Logback

Logback 当前分成 3  个模块：

* logback-core 是其他两个模块的基础模块
* logback-classic 是 Log4j 的一个改良版本。Logback-classic 完整实现 SLF4J API，使你可以很方便地更换成其他日志系统，如 Log4j 或 JDK14 Logging.
* logback-access 访问模块与 Servlet 容器集成提供通过 HTTP 来访问日志的功能。



#### 统一日志 API 的门面框架

Java 开发中经常提到面向接口编程，所以我们应该按照一套统一的 API 来进行日志编程，以实际的日志框架来实现这套 API , 这样的话，即使更换日志框架，也可以做到无缝切换。

这就是 Commons Logging  与 SLF4J  这种日志门面框架的初衷。

##### Aapache Commons Logging

Apache Comons Logging 经常被简称为 JCL， 是 Apache 开源的日志门面框架。Spring 中使用的日志框架就是 JCL.

```java
private static final Log LOGGER= LogFactory.getLog(this.getClass());

LOGGER.info("...");
```

使用 JCL 需要引入 JCL 的依赖：

```xml
<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <groupId>xx</groupId>
</dependency>
```



##### SLF4J

SLF4J  为 Java 提供了 简单日志 Facade, 允许用户以自己的喜好，在工程中通过 SLF4J 接入不同的日志实现。





### Web  MVC

Web 开发 需要一个 Web 开发框架，而 MVC 是 Web 开发中最常用的框架。从 Struts 1 到 Struts 2 , 再到现在的Spring MVC ，Jersey 等，都是 MVC 模式的实现框架。



MVC 模式将 Web 开发分为了 模型，视图以及 控制器等 3 层。



#### Spring MVC 的请求处理流程

![image-20200514151549430](D:\MarkDown 笔记\Java 工程师修炼之道\Java 工程师修炼之道.assets\image-20200514151549430.png)





## Spring

Spring : java 中用到最普遍的解决企业应用的一站式框架，基本上覆盖了 Java 开发中各种基础组件。


Spring 的常用组件：

* Spring 必需的组件，包括 Bean 容器，Context 上下文支持，Spring EL 以及相关支持工具类库。这是 Spring 的基础组件，是其他所有组件依赖的基础。
* Spring AOP 是做日志统一管理，事务管理时需要用到的组件。
* Spring ORM 中提供了 对 Hibernate 等 ORM 框架的整合组件。
* Spring JDBC 的 JdbcTemplate 是在做数据库操作时经常用到的组件
* Spring TX 提供了数据库事务管理相关的组件
* Spring Web 提供了 Web 开发时用到的 诸如 Web 工具，视图解析器等组件。其通常与 Spring WebMVC 一起使用。



### Spring 核心组件

Spring 框架的核心包括 Ioc，AOP，以及辅助工具 SpringEL 等。

Spring 的 Ioc 容器 是 ApplicationContext，常用的 ApplicationContext 如下：

* ClassPathXmlApplicationContext : 从 Classpath 路径下加载 XML 配置的上下文
* FileSystemXmlApplicationContext : 从文件系统中加载 XML 配置的上下文
* XMLWebApplicationContext :
* AnnotationConfigWebApplicationConotext



这些上下文的实例在 Spring 中被叫作 Bean.

#### Spring 的双亲上下文机制

#### Spring 中的事件机制

Spring 中提供了 事件机制，用于监听容器事件的发生，在事件发生时做一些处理工作。

1. 容器事件监听器：实现 ApplicationListener 接口的类，可以监听容器的事件，包括如下几种：
  
   * ContextStartedEvent：上下文启动事件
   * ContextRefreshedEvent：上下文刷新完毕事件
   * ContextStoppedEvent：上下文停止事件
   * ContextClosedEvent：上下文关闭事件
   
2. 具有意识的 Bean ：实现了 形如 XXAware 接口的 Bean 即为有意识的 Bean ，能够注入/获取 XX 代表的事物。如：实现了 ApplicationContextAware 接口的类，会自动注入当前的ApplicationContext.

   在 Bean 生命周期图中的 BeanNameAware 和 BeanFactoryAware 也是两个具有意识的 Bean:

   	* 实现了BeanNameAware的Bean 能够感知到自己在 BeanFactory 中注册的名称。
   	* 实现 BeanFactoryAware 的 Bean 能够感知到自己所属的 BeanFactory。



#### 面向方面编程 —— AOP

AOP 是为了解决某些场景下代码重复问题的一种编程技术，允许程序模块化横向切割关注点或横向切割典型的责任划分。其能够封装多个类中不同单元的相同功能，把应用业务逻辑和系统服务分开，经常用在日志和事务管理上。



现在利用注解 + 代码配置变得越来越流行。基本上每一个XML 配置都有相应的注解配置与之对应。常用的注解配置如下：

* @Configuration：表示此类的用途是定义 Bean
* @Bean：注解到方法上，创建，初始化一个 Bean ,对应于 `<beans>` 配置。
* @Profile：对应于 `<beans profile='XXXX'> ` 指定某个 profile 激活的 Bean 等。
* @ComponentScan：对应于配置，扫描对应的包



### Spring 数据操作框架

Spring 提供了对常用数据软件/服务操作的封装组件，包括关系型数据库，NoSQL 数据库，Redis，Elasticsearch，Cassandra 等。

#### Spring JDBC

spring JDBC 提供了对 JDBC 操作的封装，也是 Java 开发中经常用到的数据库操作工具，其核心类是 JdbcTemplate，提供了很多的 CRUD 操作。

#### Spring Data Redis

基于 Jedis 之上对 Redis 操作的封装。

#### Spring Data MongoDB

基于 Mongo Java Driver 对 MongoDB 的操作封装。

### Spring Boot

Spring 变得越来越复杂，越来越不好上手。推出Spring Boot，旨在降低使用 Spring 的门槛。其大大简化了 Spring 的配置工作，并且能够很容易地将应用打包为可独立运行的程序。

#### Spring Boot 的运行原理

1. @EnableAutoConfiguration

这个 Annotation 就是 Java Config 的典型代表，标注了这个 Annotation 的 Java 类会 以 Java 代码的形式（对应 XML 定义的形式）提供一系列的 Bean 定义和实例，结合 AnnotationConfigApplicationContext 和自动扫描功能，就可以构建一个基于 Spring 容器的 Java 应用。

标注了此注解的类会发生一系列初始化动作，分别如下：

Spring Boot 扫描到 @EnableAutoConfiguration 注解时，就使用 Spring 框架的 SpringFactoriesLoader 去扫描 classpath 下所有 META-INF/spring.factories 文件的配置信息，其中包括如下一些 callback 接口（在 前中后等 不同时机执行）

然后 Spring Boot 加载符合当前场景需要的配置类型并供当前或者下一步的流程使用，这里就是 提取以 org.springframework.boot.autoconfigure.EnableAutoConfiguration 作为 Key 标志的一系列 Java 配置类，然后将 这些 Java 配置类中的 Bean 定义加载到 Spring 容器中。

2. Spring Boot 启动

@SpringBootApplication 注解是一个 复合注解，包括了 @Configuration , @EnableAutoConfiguration 以及 @ComponentScan.



#### Spring Boot 的组成模块

Spring Boot 是由非常多的模块组成的，可以通过 POM 文件引入进来。 EnableAutoConfiguration 机制会进行插件化加载并自动配置，这里模块化机制的原理主要是 通过判断相应的类/文件 是否存在来实现的。

* spring-boot-starter-web
* spring-boot-starter-logging：SLF4J 和 logback 日志支持
* spring-boot-starter-redis
* spring-boot-starter-jdbc





## 数据存储



对于 数据存储中最常用的数据库来说，可以从存储介质上分为两种：

* 内存数据库：数据主要存储在内存中，同时也可以将数据持久化存储到硬盘中，如 Redis , H2DB
* 硬盘数据库：数据存储在硬盘上的这种数据库是最常见的。MySQL，Oracle，HBase



几种常用存储方式：

* 关系型数据库：MySQL，Oracle，PostgreSQL
* 非关系型数据库：MongoDB，CouchDB，HBase 为代表
* 缓存系统：缓存是为了解决传统文件读/写性能缓慢而产生的主要以内存为存储介质的软件。
* 搜索引擎：为搜索场景而诞生的软件，一般基于倒排索引实现。



### 关系型数据库—— MySQL

关系型数据库是采用关系模型来组织数据的数据库。所谓的关系模型就是二维表格模型，而一个共享型数据库是由 二维表格及其之间的联系所组成的一个数据组。



#### 存储引擎

MySQL 主要由 两种存储引擎，分别是 MyISAM  和 InnoDB。

1. MyISAM

MySQL 5.5 之前的默认引擎，特点如下：

* 不支持行锁，读取时对需要读到的所有表加锁，写入时则对表加排它锁
* 不支持事务
* 不支持外键
* 不支持奔溃后的安全恢复



2. InnoDB

MySQL 5.5 之后的默认引擎，特点如下：

* 支持行锁，采用 MVCC 来支持高并发，有可能死锁
* 支持事务
* 支持外键
* 支持奔溃后的安全恢复
* 不支持全文索引



由于 MyISAM 缓存有表  meta-data（行数等），因此在做 COUNT(*) 时对于一个结构很好的查询是不需要消耗多少资源的。而对于  InnoDB  来说，则没有这种缓存。当你需要行锁定，事务时，使用 InnoDB 则是更好的选择，也具有更高级的安全性。



MyISAM  和  InnoDB  使用的索引也是有区别的，前者为非聚簇索引，后者为聚簇索引。



总体来说：MyISAM 适合读密集型的表，而 InnoDB 适合写密集型的表，在数据库做主从分离的情况下，经常选择 MyISAM  引擎作为 主库的存储引擎。



#### 字符集和校对规则

字符集 ：一种从二进制编码到某类字符符号的映射。

校对规则：指某种字符集下的排序规则。MySQL 中每一个字符集都会对应一系列的校对规则。

校对规则：一般来说只需要考虑是否以大小写敏感的方式比较字符串或者是否用字符串编码的二进制形式来比较大小，其对应的校对规则的后缀分别是：_cs  _ci   和 _bin  。大小写敏感和二进制校对规则的不同之处在于：二进制校对规则直接使用 字符的字节进行比较，而大小写敏感的校对规则在多字节字符集情况下，如德语。



#### 索引

数据库索引是数据库使用中非常关键的技术，合理正确地使用索引能够大大提高数据库的查询性能。

##### 数据结构

MySQL 索引使用的数据结构主要是  BTree 索引 和哈希索引。

MySQL 的 BTree 索引使用的是 B 树中的 B+ Tree。

* MyISAM ：B+Tree 叶节点的 data 域存放的是数据记录的地址。在索引检索时，首先按照 B+Tree 搜索算法搜索索引，如果指定的 Key 存在，则取出其 data 域的值，然后以 data 域的值为地址读取相应的数据记录。这被称为 非聚簇索引。
* InnoDB：其数据文件本身就是索引文件。



##### 索引使用

常用的 MySQL 命令 ：explain ，此命令能够打印出 SQL 语句的执行计划，从而判断要执行的 SQL 语句是否能够命中索引，并做进一步调整。在 优化 SQL 查询时，最好的方式就是 使用此命令来判断执行计划是否合理。

`explain select * from test_user where id =1;`

注意：

* type： 显示了连接使用哪种类别，有无使用索引。如果为 ALL，那么说明要进行全表扫描。
* key：此列显示 MySQL 实际决定使用的键（索引）。如果没有选择索引，键是 NULL。
* Extra：此列如果出现  Using filesort （需要额外的步骤来发现如何对返回的行排序）或者 Using temporary （需要创建一个临时表来存储结果），说明查询需要优化。



1）最左前缀原则

MySQL 中的索引可以以一定顺序引用多列，这种索引叫作联合索引。

最左前缀原则：如果查询的时候查询条件精准匹配索引的左边连续一列或几列，则此列就可以被用到。



2）避免 where 字句中对字段函数施加函数，如 to_data(create_time) > xxxxx ，这会造成无法命中索引。



3）在使用 InnoDB 时使用与业务无关的自增主键，即使用逻辑主键，而不要使用业务主键。

##### 查询缓存的使用

##### 数据同步中的 Binlog

MySQL 的 Binlog 是用来做 POINT-IN-TIME 的恢复和主从复制的，由数据库上层生成，是 SQL 执行的逻辑日志，在事务提交完成后进行一次写入。



##### 事务机制

关系型数据库是需要遵循 ACID 规则的。

* A（Atomic）原子性
* C（Consistency）一致性
* I（Isolation）独立性
* D（Durability）持久性

为了达到以上的事务特性，数据库定义了几种事务隔离机制：

* 未授权读取：会产生脏读，可以读取未提交的记录，实际情况下不会使用。
* 授权读取：会存在不可重复读以及幻读的现象
* 可重复读取：解决了不可重复读的问题，会存在幻读现象。InnoDB 使用 MVVC + Gap Lock （InnoDB 行锁的一种）避免了幻读问题。
* 串行：也称可串行读，此级别下读操作会隐式获取共享锁，保证同事务间的互斥。其消除了脏读，幻读，但事务并发度急剧下降。

注意：

* 不可重复读：重点在修改，读取过的数据两次读的值不一样
* 幻读：重点在 记录数目变化，多次执行同一个查询返回的记录不完全相同。
* MySQL 默认事务级别为 Repeatable Read, 而 JDBC 的默认事务级别为 Read Committed，因此使用的时候要特别注意。



事务隔离机制的实现基于锁机制和并发调度。

并发调度使用的是 MVCC (多版本并发控制)，通过保存修改行的旧版本信息来支持并发一致性读和回滚等特性。

##### 锁机制

按照锁的粒度把数据库锁分为表级锁和行级锁。

* 表级锁：MySQL 中锁定粒度最大的一种锁，对当前操作的整张表加锁。MyISAM 和 InnoDB 引擎都支持表级锁。
* 行级锁：InnoDB 支持行级锁。包括如下这几种：
  * Record Lock ： 对索引项加锁，锁定符合条件的行，其他事务不能修改和删除加锁项。
  * Gap Lock：对索引项之间的 "间隙" 加锁，锁定记录的范围，不包含索引项本身。其他事务不能在锁范围内插入数据。
  * Next-key Lock：锁定索引项本身和索引范围。即 Record Lock 和 Gap Lock 的结合。



表级锁和行级锁可进一步划分为共享锁和排它锁，分别介绍如下：

* 共享锁（S）
* 排它锁（X）



InnoDB 中还有如下两个表级锁：

* 意向共享锁（IS）:表示事务准备给数据行加入共享锁，事务在一个数据行加共享锁前必须先取得该表的 IS 锁
* 意向排它锁（IX）:表示事务准备给数据行加入排他锁，事务在一个数据行加排它锁前必须先取得该表的 IX 锁

这里的意向锁是表级锁，表示的是一种意向，仅仅表示事务正在读或写某一行记录，在正在加行级锁才会判断是否冲突。

意向锁是 InnoDB 自动加的，不需要用户干预。



#### 大表优化

当 MySQL 单表记录数过大时，数据库的 CRUD 性能会下降，需要如下的一些优化措施：

* 限定数据的范围：比如，当用户查询订单历史数据时，可以控制在最近一个月的数据中进行筛选。
* 读/写分离：主库负责写，从库负责读。
* 缓存
* 垂直分区：用户表中既有用户的登录信息又有基本信息，可以拆分为两个单独的表做分表。
* 水平分区：保持数据表结构不变，通过某种策略存储数据分片，这样每片数据分散到不同的表或者库中，达到了分布式的目的。水平分区可以支撑非常大的数据量。



#### 高可用支持

MySQL 默认支持主从配置，可以一主一从，也可以一主多从。但是这个主从仅仅实现了 读/写 分离，并不能解决高可用的问题。

* MHA: 目前MySQL 中相对成熟的高可用解决方案。
* MMM：双主的架构方案，同时只有一个主允许写，另一个主允许读，一个主挂掉，其下面的 Slave 同时挂掉。此方案无法严格保证数据一致性，适用于对数据一致性要求不高的业务场景。

#### 使用提示

见 《Java 工程师修炼之道》p166





### 非关系型数据库

常用的 NoSQL 数据库主要由以下几种：

* KV 数据库：主要以（key，value） 键值对存储数据的数据库
* 文档数据库：总体形式上也是键值对的形式，但是值又可以有各种数据结构：数组，键值对，字符串等。
* 稀疏大数据库（列数据库）：一般是用来存储海量数据的。



CAP : 核心在于 一个分布式系统不可能同时满足以下3点，最多能够较好地同时满足两个：

* Consistency：一致性，所有节点在同一时间具有相同的数据。
* Available：可以性，每次请求都能在期望时间内获取正确的响应，但不保证获取的数据为最新数据，可以允许数据不一致。
* Partition tolerance：分区容错性，系统中任意信息的丢失或失败不会影响系统的继续运作。



基于 CAP 理论，为了获得可扩展性和高可用性，BASE 原则提出了 NoSQL 数据库的可用性及一致性的弱要求。如下：

* Basically Available ：基本可以，允许系统暂时不一致
* Soft state：软状态/柔性事务，可以理解为 ”无连接“的，暂时允许一些不准确的地方和数据的变换。
* Eventual Consistency：最终一致性，当所有服务逻辑执行完成后，系统最后将回到一个一致的状态。



#### KV 数据库

KV 数据库是主要以 （key，value）键值对存储数据的数据库，可以通过key 快速查询到其 value，包括 Redis 和 SSDB 等。

#### 文档数据库 —— MongoDB

文档数据库总体形式上也是键值对的形式，但是值又可以有各种数据结构：数组，键值对，字符串等，可以认为是 JSON 格式的数据存储，因此能够对任意字段建立索引，实现关系型数据库的某些功能。



MongoDB 是一个 分布式文件存储的数据库，是非关系型数据库中最像关系型数据库的，其具有以下特点：

* 灵活模式：数据以 JSON 格式存储。
* 高可用性：复制集保证高可用
* 可扩展性：通过 Sharded cluster 保证可扩展性。



##### 关键概念

1）数据库：对应于 MySQL 的 Database.

2）集合（Collection）：对于 MySQL 的 Table ，存在于数据库中，没有固定的结构，可以插入不同格式和类型的数据。

3）文档：对应于 MySQL 的 Row ，是一组键值对（BSON）。不需要设置相同的字段，并且相同的字段不需要相同的数据结构。

4）域：对应于 MySQL 的 Column。

5）主键：和 MySQL 相同，MongoDB 会自动将 _id 字段设置为主键。

6）复制集：MongoDB 复制集由一组 mongod 实例（进程）组成，包含一个 Primary 节点，多个 Secondary 节点以及可选择的 Arbiter 节点。











