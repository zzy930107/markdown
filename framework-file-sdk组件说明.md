# Framework-file-sdk组件说明


## 该组件功能介绍：
Framework-file-sdk实现了文件的上传、删除、下载功能，并保存文件元数据信息。通过jcifs单点登录后，获取登录域用户、获取用户名、获取用户密码，然后用户利用SMB jcifs实现对windows中的共享文件的操作。


## 基于原理：
1. 基于第三方 SMB 将本地文件上传到服务器上，服务器是windows的。

2. JCIFS的开发方法类似java的文件操作功能，它的资源url定位：smb://{user}:{password}@{host}/{path}，smb为协议名，user和password分别为共享文件机子的登陆名和密码，@后面是要访问的资源的主机名或IP地址。

3. 定义了标准文件上传下载接口，方便以后服务后端更改实现方案。

4. 文件信息表达扩展：文件 md5 值 （后期可扩展为无需用户上传，同一个文件可以直接引用）、文件修改时间等属性。
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
    
        <!--框架sdk - 缓存（提供全局的缓存） -->
        <dependencie>
        <groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
        <artifactid>framework-cache-sdk</artifactid><!-- 要依赖的artifactid -->
        </dependencie>

        <!--lombok -->
        <dependencie>
        <groupid>org.projectlombok<groupid>
        <artifactid>lombok</artifactid>
        </dependencie>

        <!--为java提供的简单日志Facade -->
        <dependencie>
        <groupid>org.slf4j<groupid>
        <artifactid>slf4j-api</artifactid>
        </dependencie>
        <!--该jar功能：Facade：门面，更底层一点说就是接口。他允许用户以自己的喜好，在工程中通过slf4j接入不同的日志系统。更直观一点，slf4j是个数据         线，一端嵌入程序，另一端链接日志系统，从而实现将程序中的信息导入到日志系统并记录。因此，slf4j入口就是众多接口的集合，他不负责具体的日志实           现，只在编译时负责寻找合适的日志系统进行绑定。具体有哪些接口，全部都定义在slf4j-api中。查看slf4j-api源码就可以发现，里面除了public final           class LoggerFactory类之外，都是接口定义。因此，slf4j-api本质就是一个接口定义-->



        <!--利用SMB jcifs操作文件 -->
        <dependencie>
        <groupid>org.samba.jcifs<groupid>
        <artifactid>jcifs</artifactid>
        </dependencie>
```

## 该组件的使用方式：
1. 在pom文件中引入如下jar包：

```xml

        <!--操作文件sdk-->  
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-file-sdk</artifactid>
	</dependencie>   
``` 

2. 在yml文件中进行如下配置：

```yml   
h2018：
  hgfos：
    aooId：1002
    appKey：666666
    appSecret：666666
```

3. yml参数配置说明：
aooId（应用标识）、appKey（应用key）、appSecret（应用密钥）需要平台组提供

4. 引入依赖后，本地应用会提供如下三个 RestFul 服务地址：

文件删除：http：//localhost:port/hgfos/del{fileId}

文件下载：http：//localhost:port/hgfos/download{fileId}

文件上传：错误，超链接引用无效
其中 fileId 为文件上传时，会生成文件的唯一标识
如果本地应用启动网关应用或者部署到开发环境调试，提供的 RestFul 地址如下：
http：//ip:port/${spring.application.name}/hgfos/del{fileId}

http：//ip:port/${spring.application.name}/hgfos/download{fileId}

http：//ip:port/zuul/${spring.application.name}/hgfos/fs
其中上传服务需要加入 zuul 服务前缀用户避免上传文件出现乱码

5. 服务接口定义：

| 服务名称                             | 服务路径                         | 入参                 | 返回值                                | 描述                                               |
| -------------------------------------| ---------------------------------| ---------------------| --------------------------------------| -------------------------------------------------- |
| 文件上传（base64 数据格式上传）      |【post】/files/vl/bs              | String data          | { Id:文件唯一标识，fileName：文件名称}| 返回 Map\<String,Object>对象包含实际应用需要的属性 |
| 文件上传 multipart/form-data 上传方式|【post】/files/vl/fs              | MutiPartFile\[] files| 返回 List\<Map\<String,Object>>       | 返回 List\<Map\<String,Object>>                    |
| 文件下载                             |【GET】/files/vl//fs/\*\*/{fileId}| id                   | Object                                |                                                    |
| 删除文件                             |【post】/files/vl/dfs/{id}        | id                   | <Map\<String,Object>                  |                                                    |
6. 定义 sdk 接口 （各个业务应用依赖 sdk jar ）
Restful 接口方法：

| url地址                  | 方法名                                                                       | 参数        | 返回值                  | 描述                                                                                                                |
| -------------------------| -----------------------------------------------------------------------------| ------------| ----------------------- | --------------------------------------------------------------------------------------------------------------------|
| /hgfos/del/{fileId}      | delFile(@PathVariable(value = "fileId", required = true) String fileId )     | 文件唯一标识| HqFosInfo               | { "code": "1","message": "文件删除成功！", "fileId": "527499761119", "hgFosPath": ""}                               |
| /hgfos/download{fileId}  | downloadFile(@PathVariable(value = "fileId", required = true) String fileId )| 文件唯一标识| ResponseEntity\<byte\[]>|                                                                                                                     |
| /hgfos/fs                | uploadFile(@RequestPart(value = "file", required = true) MultipartFile file )| 上传文件对象| HgFosInfo               | { "code": "1","message": "文件上传成功！", "fileId": "1527499761119", "hgFosPath": "/file/v1/fs/1527499761119.zip"} |

服务方法接口，类名：HgFosClientService

| 方法名                           | 参数         | 返回值                  |                                                                                       
| ---------------------------------| -------------| ------------------------| 
| delFile( String fileId )         | fileId       | HgFosInfo               |
| downloadFile(String fileId )     | fileId       | ResponseEntity\<byte\[]>|                                                                                                                     
| uploadFile ( MultipartFile file )| MultipartFile| HgFosInfo               | 

7. 设置file-service参数后，就可以调用file-service接口对文件进行删除 上传和下载
