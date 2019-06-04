# framework-exception-sdk组件说明

## 该组件功能说明：

framework-exception-sdk是用来处理全局异常的，在继承标准异常后，可以对抛出的异常进行统一处理，包含后台本地异常处理、 远程调用异常处理、前端异常处理等。业务系统通过继承和使用平台定义的异常类（BaseException、ArgumentException、BusinessException、PermissionException），对外抛出异常。平台根据异常类型自动捕获异常信息，并向业务服务调用者返回统一的响应对象 BaseException。

| 属性名称          | 属性中文说明       |
|-------------------|--------------------|
| businId           | 业务 ID            |
| code              | 异常编码           |
| codeEN            | 异常编码:英文短语  |
| serviceName       | 服务名             |
| message           | 异常信息           |	
| additionalMessage | 附加信息           |
| exceptionType     | 异常类型           |

## 基本原理：
1. 由各个业务系统抛出异常（如参数异常、业务异常、权限异常）给平台异常处理组件，然后由组件处理后根据不同的异常返回不同的异常视图。

2. BaseException：此类为海关应用云平台所有异常的基类，为运行时异常。另外还有两个异常辅助类errorinfo和接口errorenuminterface用于定义异常类型和消息。

3. ArgumentException：该类定义了平台中服务入参检查的异常，包括基础异常处理，自定义异常处理和可以继承基础异常实现。各个业务应用可以直接使用此异常，平台提供参数异常工具类ArgumentUtil供各个业务应用系统使用，处理参数是否符合要求，并抛出异常，主要用于验证参数。

4. Permissionexception：此类定义了平台中使用服务权限异常定义

| 异常代码 | 异常消息                                |          枚举类型             |
|----------|-----------------------------------------|-------------------------------|
| 01000    | 未登录或登录超时                        | NOT_LOG_IN                    |
| 01001    | 无权限                                  | NO_PERMISSION                 |
| 10002    | 用户信息为空                            | AUTH_EMPTY_ERROR              |
| 10003    | 未获取到用户标识                        | AUTH_FAILD_ERROR              |
| 10004    | 未获取到角色信息                        | POSITION_FAILD_ERROR          |
| 10005    | 未登录情况下无法更换角色                | EXPOSITION_FAILD_ERROR        |
| 10006    | 解密 TOKEN 未成功                       | DECRYPT_FAILD_ERROR           |
| 10007    | 根据用户标识获取角色失败                | GETPOSBYUSERID_FAILD_ERROR    |
| 10008    | 请检查 H4A 配置是否符合规范             | H4ACONFIG_FAILD_ERROR         |
| 10009    | 请确认是否有该功能权限                  | PERMISSION_FAILD_ERROR        |
| 10010    | 未找到用户信息                          | USERINFO_FAILD_ERROR          |
| 10011    | 加载 H4A 配置失败                       | LOADH4A_FAILD_ERROR           |
| 10012    | 加载 h4a_config.xml/sdk_config.xml 失败 | LOADH4AConfigOrsdk_FAILD_ERROR|
| 10013    | H4A 未登录                              | LOGOUT_H4A_ERROR              |
| 10014    | BASE64处理失败                          | BASE64_FAILD_ERROR            |
| 10015    | 缺少用户最近登录时间                    | RVTIME_LOST_ERROR             |
| 10016    | 用户信息已失效                          | USERINFO_TIMEOUT_ERROR        |


5. DaoException：主要用来处理数据操作异常

6. CommonException：主要为通用处理异常，可用于公共方法实现中

7. BusinessException：主要为业务处理异常


### 该组件的pom文件介绍：
1. 在pom.xml中引入如下jar包：
  
```
	在modeIVersion下引入

    <!--控制jar包版本号-->
	<parent>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-parent</artifactid><!-- 要依赖的artifactid -->
	<version>2.2.0-SNAPSHOT</version><!-- 父级的版本号 -->
	</parent>
	
	
	在dependencies下引入
	
	<!--lombok包--> 
	<dependencie>
	<groupid>org.projectlombok<groupid>
	<artifactid>lombok</artifactid>
	</dependencie>
	
	<!--google提供高效设计良好的API--> 
	<dependencie>
	<groupid>com.google.guava<groupid>
	<artifactid>guava</artifactid>
	</dependencie>
	<!--该jar功能：
	Guava是java API蛋糕上的冰激凌（精华）高效设计良好的API.被google的开发者设计，实现和使用。让java的语法实践使代码更刻度，简洁，
	动态的开发它提供了大量相关的应用类，集合，多线程，比较，字符串，输入输出，缓存，网络，原生类型，数学，反射等等百分百的单元测试，被很多的项目使用，帮助开发者专注业务逻辑而不是写java应用类，使开发节省时间，资源，提高生产力-->
	 
	        
	<!--提供更多工具类，方便开发--> 
	<dependencie>
	<groupid>org.apache.commons<groupid>
	<artifactid>commons-lang3</artifactid>
	</dependencie>
	<!--该jar功能：Lang为java.lang API提供了许多辅助工具，特别是字符串操作方法，基本数值方法，对象反射，并发，创建和序列化以及系统属性。此外，它还包含对java.util.Date的基本增强功能以及一系列专用于帮助构建方法的实用程序，例如hashCode，toString和equals-->

```

## 该组件的使用方式：
1. 在pom文件中引入如下jar包：

```xml

    <!--异常sdk-->  
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-exception-sdk</artifactid>
	</dependencie>   
``` 
2. 定义自己的异常类及服务
代码示例如下：
```       
     public class ParameterServiceException extends BaseException{
	   private static final long   serialVersioUID = 7229576394568508735L;
	   
	   public ParameterServiceException（ParameterServiceError parameterServiceError）{
	   this（parameterServiceError，null，null）;
	
	     }
	   
	}
```  
3. 定义自己的异常业务错误类型
代码示例如下：
```

	public enum ParameterServiceError implements ErrorEnumInterface{
	
	MISSING_DATASOURCE_ERROR(new ERRORInfo（"1001","未找到数据源"）)
	}
``` 
4. 业务中用 try catch 抛出异常信息。


