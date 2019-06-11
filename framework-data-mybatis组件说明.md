# Framework-data-mybatis组件说明


## 该组件功能说明：
  Framework-data-mybatis封装了一个ConvertPageHelperUtil工具类，可以对数据进行分页处理，服务类项目依赖该项目后，即可对数据进行分页处理，主要用于处理持久层的数据访问，使访问变得方便快捷，提高了查询效率性能


## 基于原理：
1. 继承了PageImpl接口，获取数据的总记录数total，返回了size。
2. ConvertPageHelperUtil工具类，提供了数据的总条数、每页条数等。
3. 使用了Spring Data 进行了分页相关的处理


## 分页查询交互说明（通过继承 spring-data 的接口实现）：

| 类型     | 接口      | 代码          |  说明               |
|---------|-----------|---------------|--------------------|
| 查询输入 | PageQuery | page          | 当前页数            |
|         |           | size          | 每页条数            |
|         |           | direction     | 排序方式            |
|         |           | properties    | 排序字段（多参）     |
| 查询输出 | Page      | number        | 当前第几页          |
|         |           | totalElements | 记录总数            |


### Spring Data简单介绍：

 使用 Spring Data 进行持久层开发大致需要的三个步骤：

1. 声明持久层的接口，该接口继承 Repository，Repository 是一个标记型接口，它不包含任何方法，当然如果有需要，Spring Data 也提供了若干 Repository 子接口，其中定义了一些常用的增删改查，以及分页相关的方法。
2. 在接口中声明需要的业务方法。Spring Data 将根据给定的策略来为其生成实现代码。
3. 在 Spring 配置文件中增加一行声明，让 Spring 为声明的接口创建代理对象。配置了 <jpa:repositories> 后，Spring 初始化容器时将会扫描 base-package 指定的包目录及其子目录，为继承 Repository 或其子接口的接口创建代理对象，并将代理对象注册为 Spring Bean，业务层便可以通过 Spring 自动封装的特性来直接使用该对象。


### 该组件pom文件介绍：
1.在pom.xml中引入如下jar包：
  
```
	在modeIVersion下引入

        <!--控制jar包版本号-->
	<parent>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-parent</artifactid><!-- 要依赖的artifactid -->
	<version>2.2.0-SNAPSHOT</version><!-- 父级的版本号 -->
	</parent>
	
	
	在dependencies下引入

	<!--lombok -->
	<dependencie>
	<groupid>org.projectlombok<groupid>
	<artifactid>lombok</artifactid>
	</dependencie>
	
	<!-- mybatis框架orm使用 -->
	<dependencie>
	<groupid>org.mybatis.spring.boot<groupid>
	<artifactid>mybatis-spring-boot-autoconfigure</artifactid>
	</dependencie>
	
	     
	<!-- mybatis的分页插件 -->
	<dependencie>
	<groupid>com.github.pagehelper<groupid>
	<artifactid>pagehelper-spring-boot-starter</artifactid>
	</dependencie>
	
	<!--使用springdata -->
	<dependencie>
	<groupid>org.springframework.data<groupid>
	<artifactid>spring-data-commons</artifactid>
	</dependencie>
	
	<!--cglib动态代理-->
	<dependencie>
	<groupid>cglib<groupid>
	<artifactid>cglib</artifactid>
	</dependencie>

```


## 该组件的使用方式：
1. 在pom文件中引入如下jar包：

```xml

        <!--分页组件-->  
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-data-mybatis</artifactid>
	</dependencie>   
``` 
2. 定义分页查询 pojo 继承 PageQuery 对象
3. 定义 mybatis 的mapper
4. @RequestBody 接收分页查询对象封装Pageable
5. 分页插件 PageHelper 拦截：PageHelper.startPage(page,size,sort);
6. 返回 spring-data 的 PageImpl

7.使用PageHelper的小例子及配置：

   7.1：编写mapper.xml文件
 
  测试工程就不复杂了，简单的查询一个表，没有条件
```

	<select id="selectByPageAndSelections" resultMap="BaseResultMap">
	    SELECT *
	    FROM doc
	    ORDER BY doc_abstract
	</select>
```

   7.2：然后在Mapper.java中编写对应的接口：
  
`
public List<Doc> selectByPageAndSelections();
`

   7.3：分页

```java

	@Service
	public class DocServiceImpl implements IDocService {
	    @Autowired
	    private DocMapper docMapper;
	
	    @Override
	    public PageInfo<Doc> selectDocByPage1(int currentPage, int pageSize) {
	        PageHelper.startPage(currentPage, pageSize);
	        List<Doc> docs = docMapper.selectByPageAndSelections();
	        PageInfo<Doc> pageInfo = new PageInfo<>(docs);
	        return pageInfo;
	    }
	}
```
以上只是一个简单的例子介绍


### 备注说明
1. page 默认值1，size 默认值5
2. cn.gov.customs.h2018.tmc.ert.pojo.EntryRiskTagQuery.java
3. cn.gov.customs.h2018.tmc.ert.service.impl.EntryRiskTagServiceImpl.java
4. resource/mapper/EntryRiskTagDao.xml中selectEntryRiskTag


#### buildPageable说明

```java
 //pageNumber：页码，从第一页开始
 //pageSize：当前页记录数，默认 DEFAULT_PAGE_SIZE=15
 //direction：排序方式/排序内容（desc，asc）
 //properties：排序字段（多个逗号分隔）
public static Pageable buildPageable （int pageNumber，int pageSize）；

public static Pageable buildPageable （int pageNumber，int pageSize，String   properties）；

public static Pageable buildPageable （int pageNumber，int pageSize，String   properties，String direction）；
```
