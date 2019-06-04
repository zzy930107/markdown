# Framework-cache-sdk组件说明


## 该组件功能介绍：
Framework-cache-sdk主要提供了ehcache作为本地缓存，它 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是一种广泛使用的开源Java分布式缓存。主要面向通用缓存,Java EE和轻量级容器。它具有内存和磁盘存储，缓存加载器,缓存扩展,缓存异常处理程序,支持REST和SOAP api等特点。Spring Boot中提供了Cache对于缓存的支持，提供了一系列的自动化配置，使我们可以非常方便的使用缓存。增加缓存功能，提高数据访问性能且可以对数据进行备份且支持通用的缓存，可以通过注解配置方式应用。业务应用可以通过配置文件添加缓存对象桶 bucket进行缓存管理。应用可以通过 spring cache 自带的通用注解方式进行缓存操作，如添加（@Cacheable），删除（@CacheEvict），更新（@CachePut）将业务缓存对象缓存到缓存对象桶中。如果各个业务应用功能需要更新自主控制缓存对象，可以通过注入缓存对象管理器 CacheManger 对需要处理的缓存对象进行新增，修改，删除等操作。


### Ehcache主要特性：
1. 快速简单
2. 多种缓存策略
3. 缓存数据有两级：内存和磁盘，无需担心容量问题
4. 缓存数据会在虚拟机重启过程中写入磁盘
5. 可以通过可插入API方式进行分布式缓存
6. 具有缓存和缓存管理器的侦听接口
7. 支持多缓存管理器实例，以及一个实例的多个缓存区域
8. 可以单独使用，可以提供简单的缓存共享


## 基于原理：
1. 平台缓存基于 spring cache 标准缓存实现，spring cache 原生支持 EHCACHE  HAZELCAST INFINISPAN JCACHE REDIS CAFFEINE SIMPLE 等缓存功能配置。

2. 平台仅封装了必要的缓存配置属性参数和自定义缓存对象管理器，通过spring boot 自动加载功能，生成本地应用缓存桶。

3. 定义了两个config统一配置类CustomCacheConfiguration和CustomCacheProperties。

4. CustomCacheConfiguration类继承了CachingConfigurerSupport的公共类，提供返回统一spring缓存管理器对象（返回缓存管理器和返回密钥生成器bean以用于注释驱动的缓存管理   返回CacheResolverbean以用于解析注释驱动的缓存管理的常规缓存  返回CacheErrorHandler用于处理与缓存相关的错误），并加上@EnableCaching，让spring自动发现并配置cacheManager。

5. CustomCacheProperties定义了缓存配置属性，如maxElementsInMemory：缓存最大个数， timeToIdleSeconds：设置对象在失效前的允许闲置时间（单位：秒），timeToLiveSeconds：设置对象在失效前允许存活时间（单位：秒）等参数。   
     
6. CacheController将CacheManager接口注入并通过cacheManager.getCache（）获取缓存，然后通过cache.clear()提供了对缓存的清理并打印出debug日志。


### 该组件的pom文件介绍：
  1.在pom.xml中引入如下jar包：
  
```xml
  
	<!--在modeIVersion下引入如下-->

    <!-- 父级依赖  控制jar包版本号 -->
    <parent>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-parent</artifactid><!-- 要依赖的artifactid -->
	<version>2.2.0-SNAPSHOT</version><!-- 父级的版本号 -->
	</parent>


    <!--在dependencies下引入如下-->

    <!--引入框架核心 -->
     <dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid><!-- 父级的组id -->
	<artifactid>framework-core</artifactid><!-- 要依赖的artifactid -->
	</dependencie>

    <!--lombok -->
    <dependencie>
	<groupid>org.projectlombok<groupid>
	<artifactid>lombok</artifactid>
	</dependencie>

    <!--启用spring缓存支持 -->
    <dependencie>
	<groupid>org.springframework.boot<groupid>
	<artifactid>spring-boot-starter-cache</artifactid>
	</dependencie>

    <!--启用EhCache作为本地缓存 -->
    <dependencie>
	<groupid>net.sf.ehcache<groupid>
	<artifactid>ehcache</artifactid>
	</dependencie>
```  

## 该组件的使用方式：
1. 在pom文件中引入如下jar包：

```xml

    <!--数据源sdk-->  
	<dependencie>
	<groupid>cn.gov.customs.h2018.framework<groupid>
	<artifactid>framework-cache-sdk</artifactid>
	</dependencie>   
``` 

2.在yml文件中进行如下配置：

```yml   
h2018：
  cache：
    type：EHCACHE
    caches：
       urlPermCache：
         maxElementsInMemory：10000
         memoryStoreEvictionPolicy :LRU
         timeToIdleSeconds:3600
         timeToLiveSeconds:3600
         diskExpiryThreadIntervalSecond :1200
```

3. yml参数配置说明：

  3.1 maxElementsInMemory ： 内存缓存中最多可以存放的元素数量。 

  3.2 memoryStoreEvictionPolicy :  内存存储与释放策略,即达到maxElementsInMemory限制时,Ehcache会根据指定策略清理内存  共有三种策略,分别为LRU(最近最少使用)、LFU(最常用的)、FIFO(先进先出)。

  3.3 timeToIdleSeconds :缓存数据在失效前的允许闲置时间(单位:秒),默认值是0表示可闲置时间无穷大,若超过这个时间没有访问此Cache中的某个元素,那么此元素将被从Cache中清除。

  3.4 timeToLiveSeconds :缓存数据的总的存活时间（单位：秒），从创建开始计时，失效结束。

  3.5 diskExpiryThreadIntervalSecond : 磁盘缓存的清理线程运行间隔,默认是120秒。

4. 在项目启动类前加上@EnableCaching注解；这样的话，启动类启动时会去启动缓存启动器。
 
5. 使用注解方式操作缓存对象：
 Spring对缓存的支持类似于对事务的支持。 使用注解标记方法，相当于定义了切点，然后使用Aop技术在这个方法的调用前、调用后获取方法的入参和返回值，进而实现了缓存的逻辑。

 @Cacheable：
  5.1 使用@Cacheable把数据存进缓存。@Cacheable可以标记在一个方法上，也可以标记在一个类上，当标记在一个方法上时表示该方法是支持缓存的，当标记在一个类上时则表示该类所有的方法都是支持缓存的。当第一次调用这个方法时，它的结果会被缓存下来，在缓存的有效时间内，以后访问这个方法都直接返回缓存结果，不再执行方法中的代码段。
 
这个注解可以用condition属性来设置条件，如果不满足条件，就不使用缓存能力，直接执行方法。可以使用key属性来指定key的生成规则。

@Cacheable 支持如下几个参数：
value：缓存位置名称，不能为空，如果使用EHCache，就是配置类中声明的cache的name, 指明将值缓存到哪个Cache中

key：缓存的key，默认为空，既表示使用方法的参数类型及参数值作为key，支持SpEL，如果要引用参数值使用井号加参数名，如：#userId，一般来说，我们的更新操作只需要刷新缓存中某一个值，所以定义缓存的key值的方式就很重要，最好是能够唯一，因为这样可以准确的清除掉特定的缓存，而不会影响到其它缓存值 ， 
本例子中使用实体加冒号再加ID组合成键的名称，如”user:1”、”order:223123”等

condition：触发条件，只有满足条件的情况才会加入缓存，默认为空，既表示全部都加入缓存。


例子如下：
      
```java

	//将缓存保存到名称为UserCache中，键为"user:"字符串加上userId值，如 'user:1'
	@Cacheable(value="UserCache", key="'user:' + #userId")    
	public User findById(String userId) {    
	    return (User) new User("1", "mengdee");           
	}  
	
	
	将缓存保存进UserCache中，并当参数userId的长度小于12时才保存进缓存，默认使用参数值及类型作为缓存的key
	保存缓存需要指定key，value， value的数据类型，不指定key默认和参数名一样如："1"
	@Cacheable(value="UserCache", condition="#userId.length() < 12")    
	public boolean isReserved(String userId) {    
	    System.out.println("UserCache:"+userId);    
	    return false;    
	}
```

@CacheEvict：
2.与@Cacheable功能相反，使用@CacheEvict清除缓存，@CacheEvict是用来标注在需要清除缓存元素的方法或类上的。当标记在一个类上时表示其中所有的方法的执行都会触发缓存的清除操作。当标记在一个方法上表明所修饰的方法是用来删除失效或无用的缓存数据。

@CacheEvict 支持如下几个参数：
value：缓存位置名称，不能为空，同上
key：缓存的key，默认为空，同上
condition：触发条件，只有满足条件的情况才会清除缓存，默认为空
allEntries：true表示清除value中的全部缓存，默认为false

例子如下：

```java

	//清除掉UserCache中某个指定key的缓存   
	@CacheEvict(value="UserCache",key="'user:' + #userId")    
	public void removeUser(User user) {    
	    System.out.println("UserCache"+user.getUserId());    
	}    
	
	//清除掉UserCache中全部的缓存   
	@CacheEvict(value="UserCache", allEntries=true)    
	public final void setReservedUsers(String[] reservedUsers) {    
	   System.out.println("UserCache deleteall");    
	}
```

@CachePut：
3.与@Cacheable不同，@CachePut不仅会缓存方法的结果，还会执行方法的代码段。它支持的属性和用法都与@Cacheable一致。


4.通过注入缓存对象操作缓存信息，取出缓存对象 Cache，通过缓存对象进行取值/删除等操作
例子如下：
   
```java

	@Autowired
	private CacheManager cacheManager;

	private static UserContextUtil userContextUtil;

	private static Cache cache;

	public void set CacheManager（CacheManager cacheManager）{
	  this.cacheManager=CacheManager;

	}

	@PostConstruct
	public void init(){
	  userContextUtil=this;
	  userContextUtil.cacheManager=this.cacheManager;
	  cache=this.cacheManager.getCache
				      //此处为配置文件中自定义缓存桶名称
	 (H2018H4aDefaultCacheConstants.DEFAULT_CACHE_NAME);

	}
```
         
