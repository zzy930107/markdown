# framework-exception-sdk组件说明

## 该组件功能说明：

framework-exception-sdk是用来处理统一异常的，在继承标准异常后，可以对抛出的异常进行统一处理，包含后台本地异常处理、 远程调用异常处理、前端异常处理等。业务系统通过继承和使用平台定义的异常类（BaseException、ArgumentException、BusinessException、PermissionException），对外抛出异常。平台根据异常类型自动捕获异常信息，并向业务服务调用者返回统一的响应对象 BaseException。

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
```java       
     public class ParameterServiceException extends BaseException{
	   private static final long   serialVersioUID = 7229576394568508735L;
	   
	   public ParameterServiceException（ParameterServiceError parameterServiceError）{
	   this（parameterServiceError，null，null）;
	
	     }
	   
	}
```  
3. 定义自己的异常业务错误类型
代码示例如下：
```java

	public enum ParameterServiceError implements ErrorEnumInterface{
	
	MISSING_DATASOURCE_ERROR(new ERRORInfo（"1001","未找到数据源"）)
	}
``` 
4. 业务中对异常信息进行处理。

### 异常定义规范

在复杂业务环境下,java自带的异常可能满足不了我们业务的需求, 这个时候我们可以自定义异常来进行对业务异常的处理;
首先,我们先对异常进行基本的解释:
1. Throwable是所有Java程序中异常处理的父类 ，有两种子类：Error和Exception。
2. Throwable是所有异常的根，java.lang.Throwable 
3. Error是错误，错误是致命的，它表示应用程序本身无法克服和恢复的一种严重问题，所以在程序中我们通常不会碰到Error，也通常不会去自定义实现它，一般为底层的不可恢复的类，java.lang.Error。 比如说内存溢出和线程死锁等系统问题，不可能指望程序能处理这样的情况，只有死的份了。
4. Exception是异常，java.lang.Exception
5. Exception：分为运行时异常(RuntimeException)和非运行时异常(非RuntimeException)。
   未检查异常（运行时异常）是因为程序员没有进行必需要的检查，因为疏忽和错误而引起的错误。几个经典的RunTimeException如下：

```   
  1.java.lang.NullPointerException;
  2.java.lang.ArithmaticException;
  3.java.lang.ArrayIndexoutofBoundsException
```

6. Runtime Exception（运行时异常）： 
当出现RuntimeException的时候，我们可以不处理。当出现这样的异常时，总是由虚拟机接管。比如：我们从来没有人去处理过NullPointerException异常，它就是运行时异常，并且这种异常还是最常见的异常之一。出现运行时异常后，如果没有捕获处理这个异常（即没有catch），系统会把异常一直往上层抛，一直到最上层，如果是多线程就由Thread.run()抛出，如果是单线程就被main()抛出。抛出之后，如果是线程，这个线程也就退出了。如果是主程序抛出的异常，那么这整个程序也就退出了。运行时异常是Exception的子类，也有一般异常的特点，是可以被catch块处理的。只不过往往我们不对他处理罢了。也就是说，你如果不对运行时异常进行处理，那么出现运行时异常之后，要么是线程中止，要么是主程序终止。正常情况下不要捕获，出现了异常就让程序中断。但是为了给客户更好的体验，我们会根据异常的不同定义相应的HTTP状态码给用户进行提示。 

例如：用户信息为空、未登录或登录超时、无权限等。


7. 非RuntimeException:
非运行时异常是RuntimeException以外的异常，类型上都属于Exception类及其子类。如IOException、SQLException等以及用户自定义的Exception异常。对于这种异常，JAVA编译器强制要求我们必需对出现的这些异常进行处理，要么用try-catch语句捕获它，要么用throws子句声明抛出它，否则程序就不能编译通过。


###### java异常处理规范
1. 不要捕获 Java 类库中定义的继承自 RuntimeException 的运行时异常类，如：
IndexOutOfBoundsException / NullPointerException，这类异常由程序员预检查来规避，保
证程序健壮性。
正例： if(obj != null) {...}
反例： try { obj.method() } catch(NullPointerException e){…}

2. 异常不要用来做流程控制，条件控制，因为异常的处理效率比条件分支低。（很少有见过把异常做条件的）

3. 对大段代码进行 try-catch，这是不负责任的表现。 catch 时请分清稳定代码和非稳
定代码，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的 catch 尽可能进行区分
异常类型，再做对应的异常处理。（大面积做try-catch，是不负责任的表现）

4. 捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它，请
将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理解的
内容。（一般捕获异常都是打印一下日志，没做太多处理） 

5. 有 try 块放到了事务代码中， catch 异常后，如果需要回滚事务，一定要注意手动回
滚事务。

6. finally 块必须对资源对象、流对象进行关闭，有异常也要做 try-catch。
说明： 如果 JDK7，可以使用 try-with-resources 方法。

7. 不能在 finally 块中使用 return， finally 块中的 return 返回后方法结束执行，不
会再执行 try 块中的 return 语句。（return是方法结束标志）

8. 捕获异常与抛异常，必须是完全匹配，或者捕获异常是抛异常的父类。
说明： 如果预期抛的是绣球，实际接到的是铅球，就会产生意外情况。（要抛的异常必须准确）

9. 方法的返回值可以为 null，不强制返回空集合，或者空对象等，必须添加注释充分
说明什么情况下会返回 null 值。调用方需要进行 null 判断防止 NPE 问题。
说明：本规约明确防止 NPE 是调用者的责任。即使被调用方法返回空集合或者空对象，对调用
者来说，也并非高枕无忧，必须考虑到远程调用失败，运行时异常等场景返回 null 的情况。

10. 避免出现重复的代码。
说明： 随意复制和粘贴代码，必然会导致代码的重复，在以后需要修改时，需要修改所有的副
本，容易遗漏。必要时抽取共性方法，或者抽象公共类，甚至是共用模块。
正例： 一个类中有多个 public 方法，都需要进行数相同的参数校验操作，这个时候请抽取相同的代码进行封装，提高代码的重用性

11. 在代码中使用“ 抛异常” 还是“ 返回错误码” ，对于公司外的 http/api 开放接口必
须使用“ 错误码” ；而应用内部推荐异常抛出；跨应用间 RPC 调用优先考虑使用 Result 方式，
封装 isSuccess、 “ 错误码” 、 “ 错误简短信息” 。
说明： 关于 RPC 方法返回方式使用 Result 方式的理由：

1）使用抛异常返回方式，调用方如果没有捕获到就会产生运行时错误。

2）如果不加栈信息，只是 new 自定义异常，加入自己的理解的 error message，对于调用
端解决问题的帮助不会太多。如果加了栈信息，在频繁调用出错的情况下，数据序列化和传输
的性能损耗也是问题。

12. 定义时区分 unchecked / checked 异常，避免直接使用 RuntimeException 抛出，更
不允许抛出 Exception 或者 Throwable，应使用有业务含义的自定义异常。推荐业界已定义过
的自定义异常，如： DaoException / ServiceException 等。

13. 防止 NPE，是程序员的基本修养，注意 NPE 产生的场景：

1） 返回类型为包装数据类型，有可能是 null，返回 int 值时注意判空
反例： public int f(){ return Integer 对象}，如果为 null，自动解箱抛 NPE。

2） 数据库的查询结果可能为 null。

3） 集合里的元素即使 isNotEmpty，取出的数据元素也可能为 null。

4） 远程调用返回对象，一律要求进行 NPE 判断。

5） 对于 Session 中获取的数据，建议 NPE 检查，避免空指针。

6） 级联调用 obj.getA().getB().getC()；一连串调用，易产生 NPE。
