# Framework-data-datasource-sdk组件说明


## 该组件功能介绍： 
Framework-data-datasource-sdk为了减少开发时数据源相关配置工作而使用Druid数据库连接池配置了多数据源，目前支持多数据源、两阶段提交，密码加密等功能，并且还使springboot和mybatis进行了整合。同时为了解决上线时会重复配置数据源信息操作，支持将数据库连接信息配置在数据库中。


### Druid 简单介绍：
Druid是阿里巴巴开源平台上一个数据库连接池实现，是 Java 的数据库连接池组件。它结合了C3P0、DBCP、PROXOOL等DB池的优点。提供了强大的监控和扩展功能，可以很好的监控DB池连接和SQL的执行情况，可以说是针对监控而生的DB连接池，比如加入了日志监控，比如可以监控 SQL，在监控业务可以查询慢查询 SQL 列表等。据说是目前最好的数据库连接池。


### Druid 核心主要包括三部分：
1. DruidDriver 代理 Driver，能够提供基于 Filter－Chain 模式的插件体系。
2. DruidDataSource 高效可管理的数据库连接池
3. SQLParser


### springboot框架与mybatis框架整合的好处：
可帮助您在Spring Boot之上快速构建MyBatis应用程序 ，可以自动检测现有的DataSource，将创建并注册的一个实例的SqlSessionFactory传递一个数据源作为使用输入SqlSessionFactoryBean的。将创建并注册SqlSessionTemplate的实例从SqlSessionFactory中获取。自动扫描映射器，将它们链接到SqlSessionTemplate并将它们注册到Spring上下文，以便将它们注入到bean中。可以构建独立应用程序。将样板减少到几乎为零。更少的XML配置。


## 基于原理：
1. 使用Druid连接池配置了多数据源。
2. 多数据源包括普通数据源和xa模式数据源
3. 使用Atomikos进行了JTA两阶段分布式事务处理
4. 使用 base64 进行加密解密功能
5. 引入 framework-exception-sdk 组件定义了数据源的一些错误信息，比如未配置主数据源、未发现数据源配置信息、设置超时间出错、用户名和用户密码不能为空等错误信息


### xa模式简单介绍：
1. XA是一个分布式事务协议。
2. XA中大致分为两部分：事务管理器和本地资源管理器。
3. 其中本地资源管理器往往由数据库实现，比如 Oracle、DB2 这些商业数据库都实现了XA接口
4. 事务管理器作为全局的调度者，负责各个本地资源的提交和回滚
5. XA 协议是可以在数据库conmit之后进行回滚的


### 该组件pom文件介绍：
  1.在pom.xml中引入如下jar包：

```
  
	在modeIVersion下引入:

    <!--控制jar包版本号-->
	<parent>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-parent</artifactid><!-- 要依赖的artifactid -->
	<version>2.2.0-SNAPSHOT</version><!-- 父级的版本号 -->
	</parent>

	在dependencies下引入：

    <!--处理全局异常的sdk组件-->
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-exception-sdk</artifactid>
	</dependencie>
 
	<!--引导程序应用上下文-->
	<dependencie>
	<groupid>org.springframework.cloud<groupid>
	<artifactid>spring-cloud-context</artifactid>
	</dependencie>
	
	<!--支持全栈式Web开发，包括Tomcat和spring-webmvc--> 
	<dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-starter-web</artifactid>
	</dependencie>
	
	<!--lombok--> 
	<dependencie>
	<groupid>org.projectlombok<groupid>
	<artifactid>lombok</artifactid>
	</dependencie>
	
	<!--阿里巴巴的数据库连接池--> 
	<dependencie>
	<groupid>com.alibaba<groupid>
	<artifactid>druid</artifactid>
	</dependencie>
	
	<!--通过Atomikos支持JTA分布式事务处理--> 
	<dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-starter-jta-atomikos</artifactid>
	</dependencie>
	
	<!--springboot框架与mybatis框架的整合--> 
	<dependencie>
	<groupid>org.mybatis.spring.boot<groupid>
	<artifactid>mybatis-spring-boot-starter</artifactid>
	</dependencie>
	
	 
	<!--Apache开源组织提供的用于操作JAVA BEAN的工具包--> 
	<dependencie>
	<groupid>commons-beanutils<groupid>
	<artifactid>commons-beanutils</artifactid>
	</dependencie> 
```


## 该组件的使用方式：
1. 在pom文件中引入如下jar包：

```xml

    <!--数据源sdk-->  
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-data-datasource-sdk</artifactid>
	</dependencie>   
``` 

2. 打开 application.yml 文件进行配置：

2.1 从数据库读取连接，配置配置库连接信息如下：
```yml
spring：
  datasource：
    url：jdbc:oracle:thin:@10.200.17.219:1521:ora12c
    driver-class-name:oracle.jdbc.driver.OracleDriver
    username:C##configuser
    password:CONFIGUSER
```

2.2 配置需要读取的数据库：
```yml
database：
  init-type：fromDb
  datasource：
    H18_HEPS:
      metaDataName：hepsuser
```

2.3 从yml文件中读取连接信息：
```yml
database：
  init-type：fromYml
  datasource：
    H18_HEPS:
      driverClassName：oracle.jdbc.driver.OracleDriver
      url：jdbc:oracle:thin:@10.200.17.219:1521:ora12c
      username:C##HEPUSER
      password:HEPUSER
```
