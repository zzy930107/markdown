# apigw 服务说明


## 该服务功能介绍：
apigw基于springcloud 的zuul实现单点登录，获取用户的基础信息。为微服务权限验证基于后端框架。起到了网关作用，网关是微服务架中不可缺少的部分，是所有业务服务的入口。服务网关统一向外提供REST API的过程中，除了zuul的服务路由 负载均衡功能的基础上，并且还结合H4A系统实现了H4A统一鉴权，如细粒度至请求的权限校验，新增白名单管理，用户信息管理，异常拦截等功能。


## 基于原理：
1. 基于 zuulfilter 实现，所有请求在网关流转结束后，按照需求转发至后台服务。    
2. 基于单点登录  统一认证原则 将 H4A 无缝接入。     
3. 基于http 协议，通过 head 头透传用户信息，在web 端通过cookie 设置失效。
4. 该项目依赖了framework-core和framework-auth-sdk 两个项目共同完成了用户的认证 鉴权 熔断 负载均衡等功能。
5. 定义了HandleUserFilter、HandleCookieFilter、HandleErrorFilter三个filter：
HandleUserFilter：将UserService和H4AConfig注入其中，获取用户的token来判断用户登录的权限，如是否为白名单、上下文是否为空、处理视图等功能，通过feign去调用其它服务。

HandleCookieFilter：将ApigwConfig和H4AConfig注入其中，获取用户cookie并打印日志。

HandleUserFilter：对cookie的一些异常处理，如权限异常、平台异常、其它异常等。

6.通过jwt解决了跨域身份验证。


### Jwt跨域身份验证简单介绍：
1. 用户向服务器发送用户名和密码。
2. 验证服务器后，相关数据（如用户角色，登录时间等）将保存在当前会话中。
3. 服务器向用户返回session_id，session信息都会写入到用户的Cookie。
4. 用户的每个后续请求都将通过在Cookie中取出session_id传给服务器。
5. 服务器收到session_id并对比之前保存的数据，确认用户的身份。

### 该服务的pom文件介绍：

1.在pom.xml中引入如下jar包：
  
```
  
	<!--在modeIVersion下引入如下-->
   
        <!-- 父级依赖  控制jar包版本号 -->
        <parent>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>h2018-dependencies</artifactid><!-- 要依赖的artifactid -->
	<version>2.2.0-SNAPSHOT</version><!-- 父级的版本号 -->
	</parent>


        <!--在dependencies下引入如下-->

        <!--引入框架核心 -->
        <dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-core</artifactid><!-- 要依赖的artifactid -->
	</dependencie>

        <!-- 引入framework-auth-sdk 项目-->
        <dependencie>
        <groupid>cn.gov.customs.h2018.framework<groupid>
        <artifactid>framework-auth-sdk</artifactid>
        </dependencie>

        <!---zuul依赖 -->
        <dependencie>
        <groupid>org.springframework.cloud<groupid>
        <artifactid>spring-cloud-starter-zuul</artifactid>
        </dependencie>

        <!---feign依赖 -->
        <dependencie>
	<groupid>org.springframework.cloud<groupid>
	<artifactid>spring-cloud-starter-feign</artifactid>
	</dependencie>

        <!---jwt依赖 -->
        <dependencie>
	<groupid>org.springframework.security<groupid>
	<artifactid>spring-security-jwtr</artifactid>
	</dependencie>
```


### 该服务的用法：
1. yml配置内容如下：依次是白名单 用户信息存放域 用户信息失效时间。
白名单，既不进行身份权限校验，又依然有透传信息功能，配置符合 antpathmatcher 规则（？匹配一个字符； \*匹配0个或多个字符； \*\*匹配0个或多个目录）
Cookie.domain 配置为前后服务共同所在域
Cookie.timeout 配置为cookie 失效时间

2. 在consul 启动情况下，启动网关

3. 将访问路径改成：网关服务地址/请求服务名/请求接口映射路径，即可正常访问
    
4. 在项目启动类加上以下注解：
```
          //springboot必备注解 
	@SpringCloudApplication

           //开启zuul
	@EnableZuulProxy 

           //让注册中心发现并扫描到该服务
	@EnableDiscoveryClient 

         //调用其它微服务
	@EnableFeignClients 
```
