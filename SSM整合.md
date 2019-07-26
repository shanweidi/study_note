# SSM整合 #

	整合思路
		使用Spring框架去整合mybatis和Springmvc框架

		1.单独搭建spring框架 
		2.单独搭建SpringMVC框架
		3.整合Spring与SpringMVC(谁帮我去加载Spring核心配件)
		4.单独搭建Mybatis框架
		5.整合Spring与Mybatis


	整合案例：
		Account  ---  表字段  id ---  name  ---money
		银行转账案例

# 1.单独完成Spring框架的搭建 #

	1.创建spring核心配置文件

	2.配置spring扫描器
		 <context:component-scan base-package="cn.itcast" >
        <!--- 忽略Controller注解 -->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
	3.添加注解


# 2.单独搭建SpringMVC框架 #

## 1）配置前端控制器 dispatcherServlet  --- post filter ##

	<filter>
	    <filter-name>characterEncodingFilter</filter-name>
	    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	  </filter>
	  <filter-mapping>
	    <filter-name>characterEncodingFilter</filter-name>
	    <url-pattern>/*</url-pattern>
	  </filter-mapping>
	
	  <servlet>
	    <servlet-name>dispatcherServlet</servlet-name>
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	    <init-param>
	      <param-name>contextConfigLocation</param-name>
	      <param-value>classpath:springmvc.xml</param-value>
	    </init-param>
	  </servlet>
	  <servlet-mapping>
	    <servlet-name>dispatcherServlet</servlet-name>
	    <url-pattern>/</url-pattern>
	  </servlet-mapping>

## 2）配置springMVC核心配置文件    SpringMVC.XML ##
		
    <context:component-scan base-package="cn.itcast">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/" />
        <property name="suffix" value=".jsp" />
    </bean>

    <mvc:annotation-driven />

## 3）controller扫描（只扫描controller） ---- 视图解析器 ---mvc注解开启（注册处理映射器-处理适配器） ##

	<context:component-scan base-package="cn.itcast">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
	
# 3.整合Spring 与 SpringMVC #

	如何整合
		1.我如何让Spring容器随着web容器加载
			监听器：做事件监听。
			ContextLoaderListener ----  帮助Spring容器去监听WEB容器是否被开启

	<!-- 配置Spring核心监听器
    监听web容器是否开启
        ContextLoaderListener 会帮spring加载applicationContext.xml
       但是有要求：必须在WEB-INF目录下，并且名字叫做applicationContext.xml
	   -->
	  <listener>
	    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	  </listener>
	  <context-param>
	    <param-name>contextConfigLocation</param-name>
	    <param-value>classpath:applicationContext.xml</param-value>
	  </context-param>



# 4.单独搭建mybatis框架 #

1.编写mybatis核心配置文件

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://192.168.130.130:3306/eesy"/>
	                <property name="username" value="root"/>
	                <property name="password" value="root"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <mappers>
	        <!--<mapper resource="org/mybatis/example/BlogMapper.xml"/>-->
	        <package name="cn.itcast.dao" />
	    </mappers>
	</configuration>

2.为dao接口添加注解
	
	// 查询所有账户
    @Select("select * from account")
    public List<Account> findAll();

# 5.整合Spring与mybatis #
	
	思路：
        如果spring整合mybatis
        帮助mybatis创建一个sqlSession工厂，基于这个工厂来得到sqlSession对象
        1.需要一个数据连接池   dataSource
        2.需要一个sqlSession工厂   SqlSessionFactoryBean
        3.需要让Spring帮我扫描所有的mapper接口  MapperScannerConfigurer

	<!-- 配置C3P0的连接池对象 -->
	<bean id="dataSource"
	class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver" />
	<property name="url" value="jdbc:mysql:///ssm" />
	<property name="username" value="root" />
	<property name="password" value="root" />
	</bean>
	<!-- 配置SqlSession的工厂 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<property name="configLocation" value="classpath:mybatis-config.xml"></property>
    <property name="mapperLocations" value="classpath:mapper/job/**"/>
	</bean>
	<!-- 配置扫描dao的包 -->
	<bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="cn.itcast.dao"/>
	</bean>

# 6.声明式事务配置 #

	<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<tx:attributes>
	<tx:method name="find*" read-only="true"/>
	<tx:method name="*" isolation="DEFAULT"/>
	</tx:attributes>
	</tx:advice>
	<aop:config>
	<aop:advisor advice-ref="txAdvice" pointcut="execution(public * cn.itcast.service.impl.*.*(..))"/>
	</aop:config>



<build>
    <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                 <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
            </resource>
     </resources>
</build>

总结：：

	1.spring如何整合SpringMVC
		思路：如何帮助Spring加载spring容器 
			  <listener>
			    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
			  </listener>
			  <context-param>
			    <param-name>contextConfigLocation</param-name>
			    <param-value>classpath:applicationContext.xml</param-value>
			  </context-param>

	2.spring整合mybatis 
		思路:
			1.配置数据连接池
			2.配置SqlSessionFactoryBean   --- 创建 SqlSessionFactory
			3.扫描DAO接口  ---  
			<bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
			<property name="basePackage" value="cn.itcast.dao"/>
			</bean>



作业：完成SSM整合 
		案例：完成用户登陆功能，用户登陆成功！展示数据库所有用户信息，每个用户信息提供CUD操作。
			login.jsp  --- > 提供用户登陆功能 ----> 当用户登陆成功后，显示所有用户信息（数据库中User表） --- >为所有学生提供一个cud操作