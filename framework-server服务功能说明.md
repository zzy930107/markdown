# Framework-server服务功能说明

## 该服务功能介绍：
Framework-server该项目作为统一的客户端后台支持，当用户登录后，通过过滤器拦截调用该项目定义好的接口并获取用户当前的信息，根据用户权限的不同，可以给用户进行信息反馈，获取用户的所有信息，包括用户权限控制和菜单，让不同的用户对不同的功能进行配置及操作，通过webservice对其它服务进行访问并进行服务熔断，缓存和协议兼容等功能，是海关应用云平台的中枢神经系统，注意该项目不能被其它服务所依赖。


## 基于原理：
1. ui权限控制：前端框架调用微服务，微服务在调用H4A的API，检查当前用户的应用和权限信息，通过这些信息控制用户的应用展示和菜单的显示。

2. 业务权限检查：用户调用框架微服务方法询问当前用户是否具有特定权限，微服务在调用H4A的API查询该用户权限信息，将结果返回给调用方。


## 权限控制功能说明：
权限控制包括业务权限检查和UI权限控制：
1. 业务权限检查：通过提供微服务业务组件调用来判断当前用户是否可以被授权操作特定的功能。
2. UI权限控制：通过导航节点的权限配置，使得用户只能看见符合自己权限的应用及菜单。


### 该组件pom文件介绍：
1.在pom.xml中引入如下jar包：
  
```
  
	<!--在modeIVersion下引入如下-->

    <!-- 父级依赖  控制jar包版本号 -->

	<parent>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>h2018-dependencies</artifactid>
	<version>2.2.0-SNAPSHOT</version>
	</parent>


    <!--在dependencies下引入如下-->

    <!-- 引入framework-core 项目-->
    <dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-core</artifactid>
	</dependencie>

	<!-- 引入framework-auth-sdk 项目-->
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-auth-sdk</artifactid>
	</dependencie>

    <!-- 引入framework-data-mybatis 项目-->
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-data-mybatis</artifactid>
	</dependencie>

	<!-- 引入framework-data-datasource-sdk项目-->
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-data-datasource-sdk</artifactid>
	</dependencie>

	<!--lombok # provide不传递-->
	<dependencie>
	<groupid>org.projectlombok<groupid>
	<artifactid>lombok</artifactid>
	</dependencie>

    <!--oracle 驱动-->
    <dependencie>
	<groupid>com.oracle.jdbc<groupid>
	<artifactid>ojdbc8</artifactid>
	</dependencie>

    <!-- mysql驱动 -->
    <dependencie>
	<groupid>mysql<groupid>
	<artifactid>mysql-connector-java</artifactid>
	</dependencie>

    <!--阿里巴巴的数据库连接池 -->
	<dependencie>
	<groupid>com.alibaba<groupid>
	<artifactid>druid</artifactid>
	</dependencie>


	<!-- mybatis框架orm使用 -->
	<dependencie>
	<groupid>org.mybatis.spring.boot<groupid>
	<artifactid>mybatis-spring-boot-autoconfigure</artifactid>
	</dependencie>

	<!-- springboot框架与mybatis框架的整合 -->
	<dependencie>
	<groupid>org.mybatis.spring.boot<groupid>
	<artifactid>mybatis-spring-boot-starter</artifactid>
	</dependencie>

	<!-- mybatis的分页插件 -->
	<dependencie>
	<groupid>com.github.pagehelper<groupid>
	<artifactid>pagehelper-spring-boot-starter</artifactid>
	</dependencie>

	<!--cglib动态代理-->
	<dependencie>
	<groupid>cglib<groupid>
	<artifactid>cglib</artifactid>
	</dependencie>
	
	<!--使用Spring Web Services-->
	<dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-starter-web-services</artifactid>
	</dependencie>
	
	<!-- Web Services   Axis运行所需要的组件包 -->
	<dependencie>
	<groupid>wsdl4j<groupid>
	<artifactid>wsdl4j</artifactid>
	</dependencie>

```