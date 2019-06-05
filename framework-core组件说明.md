# Framework-core组件说明

## 该组件功能介绍：
Framework-core组件是所有服务类项目必须依赖该模块，是所有项目的核心依赖。提供了基础的功能，比如通过ribbon进行负载均衡、hystrix进行熔断、定义线程池信息、修改http消息、利用spring 获取上下文、定义异常Exception基础类，获取各种请求的上下文，基础的格式处理等功能。

## 基于原理：
1. 封装了4个核心 util 工具类：

1.2 ConvertUtil 工具类对数据进行了分页及排序处理，pageNumber 为当前页，默认是第一页，pageSize 为当前页的数据条数，每页默认15条，根据 properties 字段对数据进行排序。

1.3 EnumNameHelper 工具类为枚举操作类。

1.4 TypeNameHelper 工具类返回类型的常量的顺序，返回包含这些常量的数组。

1.5 XmlHelper 根据 Vue的组件框Element 获取它的节点名称、节点父类接默认值。

2. 封装了两个用户工具类：

2.3 UserContextHolder 利用线程池来获取用户上下文。

2.4 UserHttpConvertUtil 通过 HttpServletRequest 获取用户的信息并通过 Base64 进行数据传递。

3. 使用 HttpClient线程池来处理大量并发发起的http消息。

4. ControllerExceptionHandler 定义了通用异常，包括业务异常、权限异常、参数异常等。


### 该组件pom文件介绍：
  1.在pom.xml中引入如下jar包：
  
```
  
	<!--在modeIVersion下引入如下-->
    
    <!-- 父级依赖  控制jar包版本号 -->
    <parent>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-parent</artifactid><!-- 要依赖的artifactid -->
	<version>2.2.0-SNAPSHOT</version><!-- 父级的版本号 -->
	</parent>


    <!--在dependencies下引入如下-->

    <!--使用tsf链路跟踪时启用 -->
    <dependencie>
    <groupid>com.tencent.tsf<groupid>
    <artifactid>spring-cloud-tsf-sleuth</artifactid>
    </dependencie>
          
    <!--springcloud consul注册发现功能 -->
    <dependencie>
    <groupid>org.springframework.cloud<groupid>
    <artifactid>spring-cloud-starter-consul-discovery</artifactid>
    </dependencie>

      
    <!--springboot 核心依赖 -->
    <dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-starter</artifactid>
	</dependencie>
	<!--功能：Spring Boot的核心启动器，包含了自动配置、日志和YAML -->
   
    <!--spring提供的监控模块 -->
    <dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-starter-actuator</artifactid>
	</dependencie>

    <dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-actuator-docs</artifactid>
	</dependencie>

    <!--该jar功能：
     spring-boot-starter-actuator模块是一个spring提供的监控模块。我们在开运行过程中，需要实时和定时监控服务的各项状态和可用性。Spring Boot的spring-boot-starter-actuator 模块（健康监控）功能提供了很多监控所需的接口，可以对应用系统进行配置查看、相关功能统计等。-->
    
    <!--springboot 提供的热部署 -->
    <dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-devtoos</artifactid>
	</dependencie>

    <!--lombok -->
    <dependencie>
	<groupid>org.projectlombok<groupid>
	<artifactid>lombok</artifactid>
	</dependencie>

    <!--google提供高效设计良好的API -->
    <dependencie>
	<groupid>com.google.guava<groupid>
	<artifactid>guava</artifactid>
	</dependencie>
	<!--该jar功能：
	 Guava是java API蛋糕上的冰激凌（精华）高效设计良好的API.被google的开发者设计，实现和使用。让java的语法实践使代码更刻度，简洁，
	动态的开发它提供了大量相关的应用类，集合，多线程，比较，字符串，输入输出，缓存，网络，原生类型，数学，反射等等百分百的单元测试，被很多的项目使用，帮助开发者专注业务逻辑而不是写java应用类，使开发节省时间，资源，提高生产力，在这里主要提供缓存的作用-->
	 

    <!--使用TSF分布式配置时启用 -->
    <dependencie>
	<groupid>org.springframework.cloud<groupid>
	<artifactid>spring-cloud-consul-config</artifactid>
	</dependencie>

    <!--提供项目的测试功能 -->
    <dependencie>
    <groupid>org.springframework.boot<groupid>
    <artifactid>spring-cloud-starter-test</artifactid>
    </dependencie>
    <!--功能：支持常规的测试依赖，包括JUnit、Hamcrest、Mockito以及spring-test模块-->

    <!--使用springdata -->
    <dependencie>
	<groupid>org.springframework.data<groupid>
	<artifactid>spring-data-commons</artifactid>
	</dependencie>

    <!--提供更多工具类，方便开发 -->
    <dependencie>
	<groupid>org.apache.commons<groupid>
	<artifactid>commons-lang3</artifactid>
	</dependencie>
    <!--功能：Lang为java.lang API提供了许多辅助工具，特别是字符串操作方法，基本数值方法，对象反射，并发，创建和序列化以及系统属性。此外，它还包含对java.util.Date的基本增强功能以及一系列专用于帮助构建方法的实用程序，例如hashCode，toString和equals。-->

        
    <!--对日期和json的处理 -->
    <dependencie>
	<groupid>com.fasterxml.jackson.datatype<groupid>
	<artifactid>jackson-datatype-jsr310</artifactid>
	</dependencie>

    <!--解析JAVA 的 xml-->
    <dependencie>
	<groupid>org.dom4j<groupid>
	<artifactid>dom4j</artifactid>
	</dependencie>
    <!--功能：dom4j是一个Java的XML API，类似于jdom，用来读写XML文件的。dom4j是一个非常非常优秀的Java XML API，具有性能优异、功能强大和极端易用使用的特点，同时它也是一个开放源代码的软件，可以在SourceForge上找到它。对主流的Java XML API进行的性能、功能和易用性的评测，dom4j无论在那个方面都是非常出色的。很多的Java软件都在使用dom4j来读写XML，这是必须使用的jar包-->
    
       
    <!--框架sdk - 缓存（提供全局的缓存） -->
    <dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-cache-sdk</artifactid><!-- 要依赖的artifactid -->
	</dependencie>
```

## 该组件的使用方式：
1. 在pom文件中引入如下jar包：

```xml

    <!--项目核心组件-->  
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-core</artifactid>
	</dependencie>   
``` 

