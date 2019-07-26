# MAVEN高级 #

一，回忆下 maven 优点 （管理jar包的）


	maven高级应用：
	1，maven基础回顾。
	2，maven传统的web工程做一个数据查询操作。
	3，maven工程拆分与聚合的思想。
	4，把第二阶段做好的web工程修改成maven拆分与聚合的形式。
	5，私服【远程仓库】。
	6，如何安装第三方jar包。【把第三方jar包安装到本地仓库，把第三方jar包安装到私服。】
	
	maven基础知识回顾：
	maven是一个项目管理工具。
	依赖管理：maven对项目中jar包的管理过程。传统工程我们直接把jar包放置在项目中。
	          maven工程真正的jar包放置在仓库中，项目中只用放置jar包的坐标。
	仓库的种类：本地仓库，远程仓库【私服】，中央仓库。
	仓库之间的关系：当我们启动一个maven工程的时候，maven工程会通过pom文件中jar包的坐标去本地仓库找对应jar包。
	                默认情况下，如果本地仓库没有对应jar包，maven工程会自动去中央仓库下载jar包到本地仓库。
			在公司中，如果本地没有对应jar包，会先从私服下载jar包，
			如果私服没有jar包，可以从中央仓库下载，也可以从本地上传。
	一键构建：maven自身集成了tomcat插件，可以对项目进行编译，测试，打包，安装，发布等操作。
	maven常用命令：clean，compile，test，package，install，deploy。
	maven三套生命周期：清理生命周期，默认生命周期，站点生命周期。
	
	1）jar包管理功能   
	2）仓库工程 --- 本地仓库  --- 中央仓库   --- 私服
	3）节省资源
		如果项目打包后，这时候的项目中，会存在真实的jar包吗？
			-在打包完成后，会基于pom文件jar依赖地址替换为真的jar包信息，放入到lib目录

	4）内置tomcat功能 （SSM 出现控制台乱码）
		Spring 5.0.2 版本支持的最低tomcat是。8.5 （springjar 降低到4.x.x  或者安装外置tomcat.5）

	clean  结束生命周期 （清空编译后的代码）
	package 打包 （项目打包）
	install 安装jar包到maven本地仓库中  
	compile 编译
	deploy 把jar包安装的远程仓库（私服）
	

# 工程和模块的区别： #

    工程不等于完整的项目，模块也不等于完整的项目，一个完整的项目看的是代码，代码完整，就可以说这是一个完整的项目
    和此项目是工程和模块没有关系。

    工程天生只能使用自己内部资源，工程天生是独立的。后天可以和其他工程或模块建立关联关系。
    模块天生不是独立的，模块天生是属于父工程的，模块一旦创建，所有父工程的资源都可以使用。

    父子工程直接，子模块天生集成父工程，可以使用父工程所有资源。
    子模块之间天生是没有任何关系的。

    父子工程直接不用建立关系，继承关系是先天的，不需要手动建立。

    平级直接的引用叫依赖，依赖不是先天的，依赖是需要后天建立的。

二,寻找传统maven工程弊端

	<!--maven工程要导入jar包的坐标，就必须要考虑解决jar包冲突。
      解决jar包冲突的方式一：
      第一声明优先原则：哪个jar包的坐标在靠上的位置，这个jar包就是先声明的。
      先声明的jar包坐标下的依赖包，可以优先进入项目中。

      maven导入jar包中的一些概念：
      直接依赖：项目中直接导入的jar包，就是该项目的直接依赖包。
      传递依赖：项目中没有直接导入的jar包，可以通过项目直接依赖jar包传递到项目中去。

      解决jar包冲突的方式二：
      路径近者优先原则。直接依赖路径比传递依赖路径近，那么最终项目进入的jar包会是路径近的直接依赖包。

      解决jar包冲突的方式三【推荐使用】：
      直接排除法。
      当我们要排除某个jar包下依赖包，在配置exclusions标签的时候，内部可以不写版本号。
      因为此时依赖包使用的版本和默认和本jar包一样。
      -->

	1）解决jar冲突
		第一种：声明优先原则	
		第二种：就近原则   直接依赖  > 传递依赖
				--直接依赖： 直接在pom文件中依赖的jar包  叫做直接依赖
				--传递依赖： 在pom文件中到导入的jar，在运行时候需要依赖其他jar，那么这种jar包叫做传递依赖
		第三种：直接排除 直接在pom文件中派出掉冲突的jar包
		  <exclusions>
	        <exclusion>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-core</artifactId>
	        </exclusion>
	      </exclusions>

	2）maven  pom标签
			<properties>
		    <spring.version>5.0.2.RELEASE</spring.version>
		    <slf4j.version>1.6.6</slf4j.version>
		    <log4j.version>1.2.12</log4j.version>
		    <shiro.version>1.2.3</shiro.version>
		    <mysql.version>5.1.6</mysql.version>
		    <mybatis.version>3.4.5</mybatis.version>
		    <spring.security.version>5.0.1.RELEASE</spring.security.version>
		  </properties>
		管理jar版本，方便统一更改

		<dependencyManagement>   锁定jar包的版本

		 <dependencies> 引入jar依赖

		<mvc:annotion:Driven/>

	原始maven工程的弊端
			1：模块的复用没有了
			2：jar版本无法统一管理

三，如何创建聚合工程
		
	父子工程关系：父子工程之间属于继承关系，继承关系是先天形成，不需要任何的依赖
				 子模块之间属于依赖关系，依赖关系需要后天建立。引入依赖

# 理解继承和聚合 #
	通常继承和聚合同时使用。
	 何为继承？
	继承是为了消除重复，如果将dao、service、web分开创建独立的工程则每个工程的pom.xml文件中的内容存在重复，比如：设置编译版本、锁定spring的版本的等，可以将这些重复的配置提取出来在父工程的pom.xml中定义。
	 何为聚合？
	项目开发通常是分组分模块开发，每个模块开发完成要运行整个工程需要将每个模块聚合在一起运行，比如：dao、service、web三个工程最终会打一个独立的war运行。



四，如何使用私服下载jar包（了解）

	第一步:： 需要在客户端即部署ssm_dao工程的电脑上配置 maven环境，并修改 settings.xml 文件，配置连接私服的用户和密码 。
	此用户名和密码用于私服校验，因为私服需要知道上传的账号和密码是否和私服中的账号和密码一致。

		<server>
			<id>releases</id>
			<username>admin</username>
			<password>admin123</password>   --- 私服用户名和密码
			</server>
			<server>
			<id>snapshots</id>
			<username>admin</username>
			<password>admin123</password>
		</server>
		
		releases 连接发布版本项目仓库
		snapshots 连接测试版本项目仓库
		
		第二步： 配置项目pom.xml
		配置私服仓库的地址，本公司的自己的jar包会上传到私服的宿主仓库，根据工程的版本号决定上传到哪个宿主仓库，如果版本为release则上传到私服的release仓库，如果版本为snapshot则上传到私服的snapshot仓库
		<distributionManagement> 
			<repository> 
			<id>releases</id>
			<url>http://localhost:8081/nexus/content/repositories/releases/</url> 
			//上传服务器地址
			</repository>
			<snapshotRepository> 
			<id>snapshots</id>
			<url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
			</snapshotRepository> 
		</distributionManagement>
		
		第三部：从私服下载jar包
		<!-- 下载jar包配置 -->
		<profile> 
			<!--profile的id -->
			<id>dev</id>
			<repositories>
				<repository> <!--仓库id，repositories可以配置多个仓库，保证id不重复 -->
					<id>nexus</id> <!--仓库地址，即nexus仓库组的地址 -->
					<url>http://localhost:8081/nexus/content/groups/public/</url> <!--是否下载releases构件 -->
					<releases>
						<enabled>true</enabled>
					</releases> <!--是否下载snapshots构件 -->
					<snapshots>
						<enabled>true</enabled>
					</snapshots>
				</repository>
			</repositories>
			<pluginRepositories> <!-- 插件仓库，maven的运行依赖插件，也需要从私服下载插件 -->
				<pluginRepository> <!-- 插件仓库的id不允许重复，如果重复后边配置会覆盖前边 -->
					<id>public</id>
					<name>Public Repositories</name>
					<url>http://localhost:8081/nexus/content/groups/public/</url>
				</pluginRepository>
			</pluginRepositories>
		</profile>

	<activeProfiles>
		<activeProfile>dev</activeProfile>
	</activeProfiles>



五，如何使用maven安装第三方jar包



	--安装第三方jar包到本地仓库

	----进入jar包所在目录运行
	mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dfile=fastjson-1.1.37.jar -Dpackaging=jar
	----打开cmd直接运行
	mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=C:\my_java\授课资料\资料：maven【高级】\安装第三方jar包\fastjson-1.1.37.jar
	
	
	--安装第三方jar包到私服
	
	--在settings配置文件中添加登录私服第三方登录信息
	<server>
	<id>thirdparty</id>
	<username>admin</username>
	<password>admin123</password>
	</server>
	----进入jar包所在目录运行
	mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=fastjson-1.1.37.jar -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty
	----打开cmd直接运行
	mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=C:\my_java\授课资料\资料：maven【高级】\安装第三方jar包\fastjson-1.1.37.jar -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty


总结:
	拆分maven工程 使用聚合 -----重点 

	私服的使用  ----  了解

	使用maven安装第三方jar包 ---重点

作业：
	
	完成一个maven工程的拆分。。 （实现Account 用户转账案例的拆分）

拓展案例：
	
	使用maven工程拆分完成SSM,银行转账案例。并且实现，转账业务逻辑处理。
		1，在转账过程中出现异常 --- 回滚事务
		2，如果用户金额不足，那么不能进行转账。


总结：
	
	解决jar冲突  ----    三种方式 位置优先  距离优先原则（直接依赖大于传递依赖）  （直接排除法）  <exclusions></exclusions> （理解）   ---nosuch ------


	回顾SSM整合案列    ----- 重点  


	maven拆分与聚合    ----- 核心重点
			为什么需要项目拆分：提高模块的复用性
			如何启动聚合工程：1直接启动父工程  2启动war(webApp)工程  3使用外置tomcat--idea

	maven的私服  ---- 了解     公司maven仓库地址  （在企业开发的过程中，需要下载jar时，直接会从私服中下载）


	