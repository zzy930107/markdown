# framwork-parent组件说明


## 该组件功能介绍：
framwork-parent使用了maven多模块功能引入了许多子模块功能，如mybatis对数据的分页等处理、缓存管理、auth单点登录等模块。来管理各个jar包版本，子项目只要声明dependencie即可，无需关心jar版本，通过合理的模块拆分，实现代码的复用，便于维护和管理，提供插件集成，用户可以根据需要配置指定的模块。


## 该组件使用方式如下（以EClipse为例）：
1. 创建一个父maven工程：
1.1 新建一个maven项目，选择存储位置，并选择创建一个简单的maven工程。

1.2 输入Group Id、Artifact Id、Packaging，packaging选择pom包，生成父工程。

1.3 删除工程中的src 目录。

2. 创建子模块：

2.1 右击父工程名---》New---》Project，然后选择新建一个maven module工程。

2.2 设置子工程名以及父工程，再设置快速创建模式。

2.3 得到子工程，设置编译的jdk。

3. 配置个模块的依赖：在parent项目的pom.xml中建立依赖管理 

在该工程pom.xml中添加如下:
```xml

	<!--将子模块对父级项目进行关联-->
	<modules>
	<!--框架核心层-->
	<module>framewoork-core</module>
	<!--框架数据层-Mybatis-->
	<module>framework-data-mybatis</module>
	<!--框架sdk-缓存-->
	<module>framework-cache-sdk</module>
	</modules>
```
只需按以上格式在modules中添加子模块即可实现父子项目关联  
