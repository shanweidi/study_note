# Tomcat&&Servlet

## 内容回顾

xml解析

## 今日内容

1. Tomcat的目录结构
2. Tomcat部署项目
3. Servlet的生命周期
4. 在IDEA中集成Tomcat

## web相关概念回顾

1. 软件架构

   C/S架构的软件:

   * Client / Server 客户端和服务器端的软件. 都是需要在PC端安装的软件. 比如 QQ,迅雷,暴风...
   * 优点: 效果炫,一部分代码写到客户端(速度快).
   * 缺点: 服务器端更新,客户端都需要更新.

   B/S架构的软件:

   * Browser / Server 浏览器端和服务器端的软件. 不需要安装单独的客户端到PC上,只需要有一个浏览器即可. 比如 京东,网银,WEBQQ,WEB迅雷...
   * 优点: 服务器端更新,客户端浏览器不需要进行更新.
   * 缺点: 效果不炫,所有的代码运行都在服务器端,导致服务器压力过大.

   > 总结:
     后面的课程都是基于浏览器向服务器发送请求的
     但是在开发中 后台服务器是可以给各个平台提供数据支撑的  Android  iOS  PC(网页)

1. 资源分类

   * 静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源.静态资源可以直接被浏览器解析
     如： html,css,JavaScript
   * 动态资源:每个用户访问相同资源后，得到的结果可能不一样。称为动态资源。动态资源被访问后，需要先转换为静态资源，在返回给浏览器
     如：servlet/jsp,php,asp....

2. 网络通信三要素
   1. IP：电子设备(计算机)在网络中的唯一标识。
   2. 端口：应用程序在计算机中的唯一标识。 0~65536
   3. 传输协议：规定了数据传输的规则
      1. 基础协议：
         1. tcp:安全协议，三次握手。 速度稍慢
         2. udp：不安全协议。 速度快

> 总结:
> 不管是静态资源还是动态资源都会放在后台服务器里面
  只要浏览器想拿数据显示就必须向服务器发送请求
  怎么区分是静态资源还是动态资源呢
  就看这个资源能不能跟数据库服务器交互

## web服务器软件

服务器：安装了服务器软件的计算机
服务器软件：接收用户的请求，处理请求，做出响应
web服务器软件：接收用户的请求，处理请求，做出响应。
在web服务器软件中，可以部署web项目，让用户通过浏览器来访问这些项目


常见的java相关的web服务器软件：
* webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
* webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
* JBOSS：JBOSS公司的，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
* Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范servlet/jsp。开源的，免费的。
  [查看网站使用的是哪个服务器](http://toolbar.netcraft.com/site_report?url=www.taobao.com#last_reboot)

JavaEE：Java语言在企业级开发中使用的技术规范的总和，一共规定了13项大的规范


> 总结:
> 啥是服务器: 就是一台电脑, 安装了一个服务器软件, MySql(数据库管理系统 ,服务器软件)  Tomcat(web服务器软件)
> 建议: 使用64位操作系统, 安装软件的目录尽量不要带中文和其他特殊字符

## Tomcat

Tomcat：web服务器软件

1. 下载：`http://tomcat.apache.org/`
2. 安装：解压压缩包即可。

  > 注意：安装目录建议不要有中文和其他特殊字符
3. 卸载：删除目录就行了
4. 启动：
   `bin/startup.bat` ,双击运行该文件即可
   访问：浏览器输入：`http://localhost:8080` 回车访问自己  `http://别人的ip:8080` 访问别人
5. 可能遇到的问题：
	1. 黑窗口一闪而过：
		* 原因： 没有正确配置`JAVA_HOME`环境变量
		* 解决方案：正确配置`JAVA_HOME`环境变量
	2. 启动报错：
	    * 暴力：找到占用的端口号，并且找到对应的进程，杀死该进程 `netstat -ano`
		* 温柔：修改自身的端口号 `conf/server.xml`
		```xml
		<Server port="8005" shutdown="SHUTDOWN">
		<Connector port="8888" protocol="HTTP/1.1"
			connectionTimeout="20000"
			redirectPort="8445" />
		<Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>
		```
	> 如果将tomcat的默认端口号修改为80, 则在访问时就不用输入端口号 , 80端口号是http协议的默认端口号

6. 关闭
	1. 正常关闭：
		* `bin/shutdown.bat`
		* `ctrl+c`
	2. 强制关闭：
		* 点击启动窗口的×
7. 配置:
   部署项目的方式：
	1. 直接将项目放到webapps目录下即可。
		* /hello：项目的访问路径-->虚拟目录
		* 简化部署：将项目打成一个war包，再将war包放置到webapps目录下。
			* war包会自动解压缩
	2. 配置`conf/server.xml`文件(不推荐使用)
		在`<Host>`标签体中配置
		`<Context docBase="D:\hello" path="/hehe" />`
		* `docBase`:项目存放的路径
		* `path`：虚拟目录
	3. 在`conf\Catalina\localhost`创建任意名称的xml文件。在文件中编写
		`<Context docBase="D:\hello" />`
		* 虚拟目录：xml文件的名称

8. Java动态项目的目录结构：
   ```java
	-- 项目的根目录(web)
		-- WEB-INF目录(注意大写)
			-- web.xml：web项目的核心配置文件
			-- classes目录：放置字节码文件的目录
			-- lib目录：放置依赖的jar包(lib必须放在WEB-INF目录下)
   ```

9. 将Tomcat集成到IDEA中，并且创建JavaEE的项目，部署项目。
   创建Javaweb项目时  选择Java EE5以上的版本
   因为Java EE5版本使用的是Servlet2.5 不支持注解开发 只支持配置文件开发
   Servlet3.0以后才支持注解开发 而我们以后的课程中都使用注解开发

   src目录是放置代码的地方 在项目发布到服务器以后就不存在了
   web目录是放置资源文件的地方 比如 html css js 图片... 项目发布以后都会存在

> 总结:
> 服务器 就是 电脑上装了个 服务器软件(tomcat)
> 服务器一旦启动就会监听你电脑的某个端口  然后等待着别人来访问它
> tomcat默认监听的端口是8080(你可以去修改它) 
> 一个端口只能被一个应用所占用
> 切记： 在公司千万千万千万不要轻易修改配置文件

## Servlet：  server applet

概念：运行在服务器端的小程序
Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。
将来我们自定义一个类，实现Servlet接口，复写方法。
Servlet可以用来接收和处理用户请求

快速入门：
1. 创建JavaEE项目
2. 定义一个类，实现Servlet接口`public class ServletDemo1 implements Servlet`
3. 实现接口中的抽象方法
4. 配置Servlet
	web.xml中配置
	```xml
	<!--配置Servlet -->
	<servlet>
		<servlet-name>demo1</servlet-name>
		<servlet-class>cn.itcast.web.servlet.ServletDemo1</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>demo1</servlet-name>
		<url-pattern>/demo1</url-pattern>
	</servlet-mapping>
	```

执行原理：
1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径
2. 查找web.xml文件，是否有对应的`<url-pattern>`标签体内容
3. 如果有，则在找到对应的`<servlet-class>`全类名
4. tomcat会将字节码文件加载进内存，并且创建其对象
5. 调用其方法


Servlet中的生命周期方法：
1. 被创建：执行init方法，只执行一次
   Servlet什么时候被创建？
   默认情况下，第一次被访问时，Servlet被创建
   可以配置执行Servlet的创建时机
   在`<servlet>`标签下配置
   第一次被访问时创建 `<load-on-startup>`的值为负数, 默认为-1
   在服务器启动时，创建 `<load-on-startup>`的值为0或正整数, 值越小优先级越高

   Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的
   多个用户同时访问时，可能存在线程安全问题。
   解决：尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对修改值

2. 提供服务：执行service方法，执行多次
   每次访问Servlet时，Service方法都会被调用一次。

3. 被销毁：执行destroy方法，只执行一次
	* Servlet被销毁时执行。服务器关闭时，Servlet被销毁
	* 只有服务器正常关闭时，才会执行destroy方法。
	* destroy方法在Servlet被销毁之前执行，一般用于释放资源

> 总结:
> Servlet什么时候创建
  默认是第一次访问这个Servlet的时候才会被创建 而且只会被创建一次 也就是init方法只会被执行一次
  可以设置`<load-on-startup>2</load-on-startup>` 指定服务器启动时加载该Servlet
  这个数值越小  优先级越高

  service方法什么时候被执行
  只要有用户来访问这个Servlet 就会开辟一个子线程来执行这个servlet的service方法
  因为是多个线程来访问同一个Servlet 那怎么解决线程安全问题
  为了避免造成线程安全问题 尽量不要使用成员变量 如果非要用 就只是使用 不要进行修改值

  Servlet什么时候被销毁
  当服务器正常关闭的时候servlet就会被销毁 destroy方法就会执行
  如果服务器是非正常关闭  servlet也会被销毁 但是destroy方法不会被执行 

## Servlet3.0

支持注解配置。可以不需要`web.xml`了

使用步骤：
1. 创建JavaEE项目，选择Servlet的版本3.0以上，可以不创建web.xml
2. 定义一个类，实现Servlet接口
3. 复写方法
4. 在类上使用@WebServlet注解，进行配置 `@WebServlet("资源路径")`

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebServlet {
	String name() default "";//相当于<Servlet-name>

	String[] value() default {};//代表urlPatterns()属性配置

	String[] urlPatterns() default {};//相当于<url-pattern>

	int loadOnStartup() default -1;//相当于<load-on-startup>

	WebInitParam[] initParams() default {};

	boolean asyncSupported() default false;

	String smallIcon() default "";

	String largeIcon() default "";

	String description() default "";

	String displayName() default "";
}
```


## IDEA与tomcat的相关配置
1. IDEA会为每一个tomcat部署的项目单独建立一份配置文件
   查看控制台的log：Using CATALINA_BASE:   "C:\Users\fqy\.IntelliJIdea2018.1\system\tomcat\_itcast"

2. 工作空间项目 和 tomcat部署的web项目
   tomcat真正访问的是“tomcat部署的web项目”，"tomcat部署的web项目"对应着"工作空间项目" 的web目录下的所有资源
   WEB-INF目录下的资源不能被浏览器直接访问
3. 断点调试：使用"小虫子"启动 dubug 启动


获取请求参数:`String username = servletRequest.getParameter("username");`

## 今日重点

1. tomcat的目录结构
   - bin 可以执行的文件 启动/关闭tomcat
   - conf  放置配置文件
   - webapps 放置项目
   - work 放置运行时生成的文件
2. 什么是Servlet
   运行在tomcat里面的一个Java小程序
   其实本质是一个接口
3. Servlet的生命周期
   什么时候创建: 第一次访问这个Servlet就会创建 而且init方法就会被执行
   什么时候执行service: 每次访问都会执行service方法 而且是子线程
   什么时候销毁: 服务器关闭的时候servlet就销毁了 如果是正常关闭则执行destroy方法
4. Servlet的执行流程(配置文件方式)
5. 完成登陆案例