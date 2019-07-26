# Servlet&HTTP&Request

## 课程回顾

服务器: 一台电脑装了一个服务器软件
服务器软件: Tomcat
Tomcat可以干嘛:　只要一启动就会监听这台电脑的某个端口　等着别人来访问它
默认监听的端口是8080

静态资源: html  css  js  图片  视频...
动态资源: Servlet  JSP...

Servlet:　运行在服务器内的一个小程序
本质是一套接口
怎么去写一个Servlet：　写一个类去实现Servlet接口
Servlet可以干嘛：　Servlet可以接收用户的参数　给用户做出响应
Servlet的生命周期：
什么时候会被创建
什么时候会执行service方法
什么时候被销毁

## 今日内容

1. Servlet
2. HTTP协议：请求　响应
3. Request

## Servlet

1. 概念
   Servlet就是一个运行在WEB服务器上的小的Java程序,用来接收和响应从客户端发送过来的请求,通常使用HTTP协议. Servlet就是SUN公司提供的一个动态网页开发技术.

   当客户端请求静态资源时，Web服务器会自己处理并返回，当客户端请求动态资源时，Web服务器会把请求转交给扩展程序来处理，并将扩展程序的处理结果返回给客户端。
   Servlet的作用：
   用来处理从客户端浏览器发送的请求,并且可以对请求作出响应
2. 书写步骤
   第一步：编写一个类实现Servelet接口
   第二步：配置Servlet的访问路径（WEB-INF-->web.xml）
   ==Servlet的映射路径不能重复==
   Servlet的映射路径暂时必须以“/”开始
   xml中配置方式
   ```xml
   <!-- 配置Servlet -->
   <servlet>
   <!-- Servlet的名称 -->
   <servlet-name>test1</servlet-name>
   <!-- SErvlet的全路径 -->
   <servlet-class>com.itheima.a_servlet.ServletDemo1</servlet-class>
   </servlet>
   <!-- Servlet的映射 -->
   <servlet-mapping>
   <!-- Servlet的名称 -->
   <servlet-name>test1</servlet-name>
   <!-- Servlet的访问路径 -->
   <url-pattern>/ServletDemo1</url-pattern>
   </servlet-mapping>
   <!--访问的路径-->
   <!--http://localhost:8080/工程名称/ServletDemo1-->
   ```
   注解的配置方式
   `@WebServlet("/ServletDemo1")`
3. 执行原理
   当用户发送了一个请求到Tomcat服务器
   如果这个请求是静态资料就根据请求路径去找对应的资源文件
   如果找到就返回 如果找不到就返回404
   如果这个请求是一个动态资源(Servlet)
   从内存中找到url-pattern对应的路径
   如果没有就返回404
   如果有根据`<url-pattern>` 解析对应的`<servlet-name>`
   解析完`<servlet-name>`再去解析servlet标签下面的`<servlet-class>`
   解析到`<servlet-class>`之后使用反射创建Servlet对象
   再然后调用Servlet的service()方法
4. 生命周期
   生命周期: 就是一个对象从创建到销毁的过程.
   Servlet生命周期: Servlet从创建到销毁的过程.

   何时创建:用户第一次访问Servlet创建Servlet的实例, 只会被创建一次, 所以是单例
   何时销毁:当项目从服务器中移除的时候，或者关闭服务器的时候.

   ==用户第一次访问Servlet的时候,服务器会创建一个Servlet的实例,那么Servlet中init方法就会执行.任何一次请求服务器都会创建一个新的线程访问Servlet中的service的方法.在service方法内部根据请求的方式的不同调用doXXX的方法.(get请求调用doGet,post请求调用doPost).当Servlet中服务器中移除掉,或者关闭服务器,Servlet的实例就会被销毁,那么destroy方法就会执行.==

   1. Servlet生命周期分为三个阶段，初始化阶段，运行阶段，销毁阶段
   2. 初始化阶段默认在Servlet第一次被访问的时候执行，调用init方法执行一些初始化准备工作。浏览器每次发起请求则执行运行阶段调用service方法，执行具体业务流程。当服务器关闭或者项目被移除执行销毁阶段，调用destory方法，可以在destory方法中执行资源回收，连接关闭等工作。
   3. 初始化阶段和销毁阶段在整个生命周期过程中只执行一次，运行阶段可以执行多次

5. Servlet3.0 注解配置
   `@webservlet("/demo")`
6. Servlet的体系结构
   Servlet -- 接口(方法比较多 不推荐)
        |
   GenericServlet -- 抽象类()
        |
   HttpServlet  -- 抽象类

   Servlet:接口-->GenericServlet:通用的Servlet-->HttpServlet(doGet()/doPost()):HttpServlet
   GenericServlet是一个通用的，不特定于任何协议的Servlet,它实现了Servlet接口。而HttpServlet继承于GenericServlet，因此HttpServlet也实现了Servlet接口。所以我们定义Servlet的时候只需要继承HttpServlet即可。

   GenericServlet：将Servlet接口中其他的方法做了默认空实现，只将`service()`方法作为抽象
   将来定义Servlet类时，可以继承GenericServlet，实现`service()`方法即可
   HttpServlet：对http协议的一种封装，简化操作

   不管是哪一种方式实现的Servlet 如果有请求必然执行`service()`方法
   如果是继承自HttpServlet 重写`doGet()` `doPost()`
   请求到达指定的Servlet去执行其中的Service()方法 但是找不到 然后会去父类(HttpServlet)中找
   在HttpServlet的service()方法中根据请求方式调用对应的方法

7. Servlet相关配置(urlpartten:Servlet访问路径)
   1. 一个Servlet可以定义多个访问路径 ： `@WebServlet({"/d4","/dd4","/ddd4"})`
   2. 路径定义规则：
      1. `/xxx`：完全路径匹配(最常用) ("/demo1" "/user/demo1") http://localhost:8080/day14/demo1 
      2. `/xxx/*`: 目录匹配 ("/*" "/user/*")
      3. `*.do`：扩展名匹配
   3. 优先级
      完全路径匹配 > 目录匹配 > 扩展名匹配
      `/user/demo1`(1)
      `/user/*`    (2)
      `*.do`      (3)
      `http://localhost:8080/day14/user/demo1`
       ==错误的写法 ：`/*.do`==

> 总结：
> 昨天写Servlet 实现Servlet接口 必须把接口中所有的方法都给实现了 其实我们只需要service方法
> 今天给大家介绍了两个Servlet接口的实现类 GenericServlet HTTPServlet
> HTTPServlet是基于HTTP请求封装的一个Servlet 里面给大家提供了很多的API　方便我们去出来　请求和响应
> GenericServlet ：不管可以处理HttpServlet还可以搞其他的协议　但是目前就只有HTTP协议
> 现在再去写Servlet　直接继承HTTPServlet就可以了
> 在Java中一般子类都是对父类的扩充　子类一般都会比父类有更多的功能(API)
> 如果有请求到达Servlet 就只会去找service方法 如果当前的Servlet中没有service方法 会去该Servlet的父类中去找 (HTTPServlet)
> 在HttpServlet中有service方法 而且该方法的实现是 根据不同的请求方法调用不同的doXX方法
> 而子类的Servlet重写了doXX()方法 所以就会执行子Servlet中的doXX方法
> 什么请求是get :浏览器地址栏直接输入地址  点击超链接跳转页面 location.href=""  form表单如果不指定method默认是get请求..
> 什么请求是post :　form表单如果手动指定method是POST请求..
> urlpartten的配置方式
> /demo1  完全路径匹配
> /user/*  目录匹配
> .do     扩展名匹配
> 我有个Servlet  urlpartten = /*
> localhost:8080/day14/user/haha
> localhost:8080/day14/xxxx
> 一个请求只能匹配到一个Servlet 如果一个请求匹配了多个Servlet  服务器会报错
> 完全路径匹配 > 目录匹配 > 扩展名匹配


## Servlet总结

1. 什么是Servlet(概念/作用)
2. 怎么去定义一个Servlet(写法)
3. 如果访问一个Servlet(路径配置问题)
4. Servlet的执行流程(访问过程)
5. Servlet的生命周期(创建/销毁)

## TCP/IP

TCP/IP（Transmission Control Protocol/Internet Protocol）即传输控制协议/网间协议，定义了主机如何连入因特网及数据如何再它们之间传输的标准，从字面意思来看TCP/IP是TCP和IP协议的合称，但实际上TCP/IP协议是指因特网整个TCP/IP协议族。不同于OSI模型的七个分层，TCP/IP协议参考模型把所有的TCP/IP系列协议归类到四个抽象层中
应用层：TFTP，HTTP，SNMP，FTP，SMTP，DNS，Telnet 等等
传输层：TCP，UDP
网络层：IP，ICMP，OSPF，EIGRP，IGMP
数据链路层：SLIP，CSLIP，PPP，MTU
每一抽象层建立在低一层提供的服务上，并且为高一层提供服务，看起来大概是这样子的
![201710121244_542](https://i.loli.net/2018/12/02/5c03bf1958971.png)
建立TCP连接的三次握手
![201710121247_95](https://i.loli.net/2018/12/02/5c03bf30b0d72.png)
![201712240822_837](https://i.loli.net/2018/12/02/5c03bf43c39a5.png)

## HTTP

HTTP：超文本传输协议(HyperText Transfer Protocol)
Http协议是建立在TCP协议基础之上的，当浏览器需要从服务器获取网页数据的时候，会发出一次Http请求。Http会通过TCP建立起一个到服务器的连接通道，当本次请求需要的数据完毕后，Http会立即将TCP连接断开，这个过程是很短的。
在HTTP 1.0中客户端的每次请求都要求建立一次单独的连接，在处理完本次请求后，就自动释放连接。
从HTTP/1.1起，默认都开启了Keep-Alive，保持连接特性，可以在一次连接中处理多个请求，并且多个请求可以重叠进行，不需要等待一个请求结束后再发送下一个请求。简单地说，当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。虽然这里使用TCP连接保持了一段时间，但是这个时间是有限范围的，到了时间点依然是会关闭的，所以我们还把其看做是每次连接完成后就会关闭。
Http连接是一种短连接，是一种无状态的连接。所谓的无状态，是指浏览器每次向服务器发起请求的时候，不是通过一个连接，而是每次都建立一个新的连接。如果是一个连接的话，服务器进程中就能保持住这个连接并且在内存中记住一些信息状态。而每次请求结束后，连接就关闭，相关的内容就释放了，所以记不住任何状态，称为无状态连接。
如果要保持客户端程序的在线状态，需要不断地向服务器发起连接请求。通常的做法是即使不需要获得任何数据，客户端也保持每隔一段固定的时间向服务器发送一次“保持连接”的请求，服务器在收到该请求后对客户端进行回复，表明知道客户端“在线”。若服务器长时间无法收到客户端的请求，则认为客户端“下线”，若客户端长时间无法收到服务器的回复，则认为网络已经断开。
通过Session, Cookie等相关技术，也能保持一些用户的状态。但是还是每次都使用一个连接，依然是无状态连接。
Http协议指定的端口是 80，所以一般计算机上不会限制这个端口，所以Http协议能够顺利通过所有机器上的防火墙。

## Socket

套接字（socket）是通信的基石，是支持TCP/IP协议的网络通信的基本操作单元。它是网络通信过程中端点的抽象表示，包含进行网络通信必须的五种信息：
连接使用的协议， 本地主机的IP地址，本地进程的协议端口，远地主机的IP地址，远地进程的协议端口。
应用层通过传输层进行数据通信时，TCP会遇到同时为多个应用程序进程提供并发服务的问题。多个TCP连接或多个应用程序进程可能需要通过同一个 TCP协议端口传输数据。为了区别不同的应用程序进程和连接，许多计算机操作系统为应用程序与TCP/IP协议交互提供了套接字(Socket)接口。应用层可以和传输层通过Socket接口，区分来自不同应用程序进程或网络连接的通信，实现数据传输的并发服务。
创建Socket连接时，可以指定使用的传输层协议，Socket可以支持不同的传输层协议（TCP或UDP），当使用TCP协议进行连接时，该Socket连接就是一个TCP连接。
socket是在应用层和传输层之间的一个抽象层，它把TCP/IP层复杂的操作抽象为几个简单的接口供应用层调用已实现进程在网络中通信。
![201710121245_575](https://i.loli.net/2018/12/02/5c03bf515c7d3.png)

## HTTP，socket，TCP/IP：

网络由下往上分为：物理层、数据链路层、网络层、传输层、会话层、表示层和应用层。
IP协议对应于网络层，TCP协议对应于传输层，而HTTP协议对应于应用层，三者从本质上来说没有可比性
socket则是对TCP/IP协议的封装和应用(程序员层面上)。
TPC/IP协议是传输层协议，主要解决数据如何在网络中传输
HTTP是应用层协议，主要解决如何包装数据。
关于TCP/IP和HTTP协议的关系，网络有一段比较容易理解的介绍：“我们在传输数据时，可以只使用(传输层)TCP/IP协议，但是那样的话，如果没有应用层，便无法识别数据内容。如果想要使传输的数据有意义，则必须使用到应用层协议。应用层协议有很多，比如HTTP、FTP、TELNET等，也可以自己定义应用层协议。WEB使用HTTP协议作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发到网络上。”
socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口(API)。
通过Socket，我们才能使用TCP/IP协议。实际上，Socket跟TCP/IP协议没有必然的联系。Socket的出现只是使得程序员更方便地使用TCP/IP协议栈而已，是对TCP/IP协议的抽象，从而形成了我们知道的一些最基本的函数接口，比如create、listen、connect、accept、send、read和write等等。
关于socket和TCP/IP协议关系的说法比较容易理解：“TCP/IP只是一个协议栈，就像操作系统的运行机制一样，必须要具体实现，同时还要提供对外的操作接口。这个就像操作系统会提供标准的编程接口，比如win32编程接口一样，TCP/IP也要提供可供程序员做网络开发所用的接口，这就是Socket编程接口。”
有个比较形象的描述：HTTP是轿车，提供了封装或者显示数据的具体形式;Socket是发动机，提供了网络通信的能力。

## HTTP请求格式

1. 请求行
   请求方式 请求url 请求协议/版本
   `GET /login.html	HTTP/1.1`
   请求方式：
   HTTP协议有7中请求方式，常用的有2种
   * GET：
    1. 请求参数在请求行中，在url后。
    2. 请求的url长度有限制的
    3. 不太安全
   * POST：
    1. 请求参数在请求体中
    2. 请求的url长度没有限制的
    3. 相对安全

   GET和POST的区别:

   * GET的提交的参数会显示到地址栏上,而POST不显示.
   * GET往往是有大小限制的,而POST没有大小的限制.
   * GET没有请求体,而POST有请求体.

    补充:
   HTTP协议未规定GET和POST的长度限制
   GET的最大长度显示是因为浏览器和web服务器限制了URI的长度
   不同的浏览器和WEB服务器，限制的最大长度不一样
   浏览器限制
   IE 和 Safari 浏览器限制2k
   Opera 限制4k
   Firefox 限制8k
   如果超出了最大长度，大部分的服务器直接截断，也有一些服务器会报414错误。
   服务器限制
   Apache能接受url长度限制为8192字符
   Microsoft Internet Information Server(IIS)能接受url长度限制为16384个字符（可修改）。
   ngnix可以通过修改配置来改变url请求串的url长度限制。
   `client_header_buffer_size` 默认值：client_header_buffer_size 1k
   `large_client_header_buffers` 默认值 ：large_client_header_buffers 4 4k/8k

   POST是没有大小限制的。HTTP协议规范也没有进行大小限制，起限制作用的是服务器的处理程序的处理能力。
2. 请求头：客户端浏览器告诉服务器一些信息
   请求头名称: 请求头值
   常见的请求头：
   1. User-Agent：浏览器告诉服务器，我访问你使用的浏览器版本信息
      可以在服务器端获取该头的信息，解决浏览器的兼容性问题
   2. Referer：`http://localhost/login.html`
      告诉服务器，我(当前请求)从哪里来？
      作用：
      1. 防盗链：
      2. 统计工作：
3. 请求空行
   空行，就是用于分割POST请求的请求头，和请求体的。
4. 请求体(正文)：
   封装POST请求消息的请求参数的

5. 字符串格式
   ```json
   POST /login.html	HTTP/1.1
   Host: localhost
   User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
   Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
   Accept-Encoding: gzip, deflate
   Referer: http://localhost/login.html
   Connection: keep-alive
   Upgrade-Insecure-Requests: 1
   username=zhangsan
   ```
> 总结:
> 语义化标签 
> get  post  是传输数据的两种方式  都可以传输数据 都没有大小限制
> get会把请求参数放到URL上面　　而post请求则会把请求参数放到请求体中
> get其实是为了获取数据  post其实为了提交数据 
> 为什么人家都说get请求会有大小限制啊  因为get请求把参数拼接到URL上面了 而浏览器对URL的长度支持是有限制的
> 而post请求会把参数放到请求体中  HTTP请求中对请求体是没有大小限制的



## Request

1. request对象和response对象的原理
   1. request和response对象是由服务器创建的。我们来使用它们
   2. request对象是来获取请求消息，response对象是来设置响应消息

2. request对象继承体系结构
   ServletRequest		--	接口
      |	继承
   HttpServletRequest	-- 接口
      |	实现
   org.apache.catalina.connector.RequestFacade 类(tomcat)

3. request功能：
   1. 获取请求消息数据
   2. 获取请求行数据 `GET /day14/demo1?name=zhangsan HTTP/1.1`
       ==获取请求方式== ：GET `String getMethod()`
       ==获取虚拟目录==：/day14 `String getContextPath()`
       获取Servlet路径: /demo1 `String getServletPath()`
       获取get方式请求参数：name=zhangsan `String getQueryString()`
       ==获取请求URI==：/day14/demo1
       `String getRequestURI()`:		/day14/demo1
       `StringBuffer getRequestURL()`  :http://localhost/day14/demo1
          URL:统一资源定位符 ： http://localhost/day14/demo1	中华人民共和国
          URI：统一资源标识符 : /day14/demo1					共和国
       获取协议及版本：HTTP/1.1 `String getProtocol()`
       获取客户机的IP地址：`String getRemoteAddr()`
   3. 获取请求头数据
       `String getHeader(String name)`:通过请求头的名称获取请求头的值
       `Enumeration<String> getHeaderNames()`:获取所有的请求头名称
   4. 获取请求体数据:
       请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
       1. 获取流对象
          `BufferedReader getReader()`：获取字符输入流，只能操作字符数据
          `ServletInputStream getInputStream()`：获取字节输入流，可以操作所有类型数据
       2. 再从流对象中拿数据
   5. 其他功能：
      1. 获取请求参数通用方式：不论get还是post请求方式都可以使用下列方法来获取请求参数
         `String getParameter(String name)`:根据参数名称获取参数值    username=zs&password=123
         `String[] getParameterValues(String name)`:根据参数名称获取参数值的数组  hobby=xx&hobby=game
         `Enumeration<String> getParameterNames()`:获取所有请求的参数名称
         `Map<String,String[]> getParameterMap()`:获取所有参数的map集合
         中文乱码问题：
           get方式：tomcat 8 已经将get方式乱码问题解决了
           post方式：会乱码
           解决：在获取参数前，设置request的编码`request.setCharacterEncoding("utf-8");`

           注：上面那个方法仅是设置请求体的解码字符集

      2. 请求转发：一种在服务器内部的资源跳转方式
         1. 步骤：
            1. 通过request对象获取请求转发器对象：`RequestDispatcher getRequestDispatcher(String path)`
            2. 使用RequestDispatcher对象来进行转发：`forward(ServletRequest request, ServletResponse response)`

         2. 特点：
            1. 浏览器地址栏路径不发生变化
            2. 只能转发到当前服务器内部资源中。
            3. 转发是一次请求

      3. 共享数据：
         * 域对象：一个有作用范围的对象，可以在范围内共享数据
         * request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
         * 方法：
           1. `void setAttribute(String name,Object obj)`:存储数据
           2. `Object getAttitude(String name)`:通过键获取值
           3. `void removeAttribute(String name)`:通过键移除键值对

      4. 获取ServletContext：
         * ServletContext getServletContext()

## 案例：用户登录

用户登录案例需求：
1.编写login.html登录页面
	username & password 两个输入框
2.使用Druid数据库连接池技术,操作mysql，day14数据库中user表
3.使用JdbcTemplate技术封装JDBC
4.登录成功跳转到SuccessServlet展示：登录成功！用户名,欢迎您
5.登录失败跳转到FailServlet展示：登录失败，用户名或密码错误


开发步骤
1. 创建项目，导入html页面，配置文件，jar包
2. 创建数据库环境
   ```sql
   CREATE DATABASE day14;
   USE day14;
   CREATE TABLE USER(

   	id INT PRIMARY KEY AUTO_INCREMENT,
   	username VARCHAR(32) UNIQUE NOT NULL,
   	PASSWORD VARCHAR(32) NOT NULL
   );
   ```
3. 创建包`cn.itcast.domain`,创建类User
   ```java
   package cn.itcast.domain;
   /**
   	* 用户的实体类
   	*/
   public class User {

   	private int id;
   	private String username;
   	private String password;


   	public int getId() {
   		return id;
   	}

   	public void setId(int id) {
   		this.id = id;
   	}

   	public String getUsername() {
   		return username;
   	}

   	public void setUsername(String username) {
   		this.username = username;
   	}

   	public String getPassword() {
   		return password;
   	}

   	public void setPassword(String password) {
   		this.password = password;
   	}

   	@Override
   	public String toString() {
   		return "User{" +
   				"id=" + id +
   				", username='" + username + '\'' +
   				", password='" + password + '\'' +
   				'}';
   	}
   }
   ```
4. 创建包`cn.itcast.util`,编写工具类JDBCUtils
   ```java
   package cn.itcast.util;

   import com.alibaba.druid.pool.DruidDataSourceFactory;

   import javax.sql.DataSource;
   import javax.xml.crypto.Data;
   import java.io.IOException;
   import java.io.InputStream;
   import java.sql.Connection;
   import java.sql.SQLException;
   import java.util.Properties;

   /**
   	* JDBC工具类 使用Durid连接池
   	*/
   public class JDBCUtils {

   	private static DataSource ds ;

   	static {

   		try {
   			//1.加载配置文件
   			Properties pro = new Properties();
   			//使用ClassLoader加载配置文件，获取字节输入流
   			InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
   			pro.load(is);

   			//2.初始化连接池对象
   			ds = DruidDataSourceFactory.createDataSource(pro);

   		} catch (IOException e) {
   			e.printStackTrace();
   		} catch (Exception e) {
   			e.printStackTrace();
   		}
   	}

   	/**
   		* 获取连接池对象
   		*/
   	public static DataSource getDataSource(){
   		return ds;
   	}


   	/**
   		* 获取连接Connection对象
   		*/
   	public static Connection getConnection() throws SQLException {
   		return  ds.getConnection();
   	}
   }
   ```
5. 创建包cn.itcast.dao,创建类UserDao,提供login方法
   ```java	
   package cn.itcast.dao;

   import cn.itcast.domain.User;
   import cn.itcast.util.JDBCUtils;
   import org.springframework.dao.DataAccessException;
   import org.springframework.jdbc.core.BeanPropertyRowMapper;
   import org.springframework.jdbc.core.JdbcTemplate;

   /**
   	* 操作数据库中User表的类
   	*/
   public class UserDao {

   	//声明JDBCTemplate对象共用
   	private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

   	/**
   		* 登录方法
   		* @param loginUser 只有用户名和密码
   		* @return user包含用户全部数据,没有查询到，返回null
   		*/
   	public User login(User loginUser){
   		try {
   			//1.编写sql
   			String sql = "select * from user where username = ? and password = ?";
   			//2.调用query方法
   			User user = template.queryForObject(sql,
   					new BeanPropertyRowMapper<User>(User.class),
   					loginUser.getUsername(), loginUser.getPassword());


   			return user;
   		} catch (DataAccessException e) {
   			e.printStackTrace();//记录日志
   			return null;
   		}
   	}
   }
   ```
6. 编写`cn.itcast.web.servlet.LoginServlet`类
   ```java
   package cn.itcast.web.servlet;

   import cn.itcast.dao.UserDao;
   import cn.itcast.domain.User;

   import javax.servlet.ServletException;
   import javax.servlet.annotation.WebServlet;
   import javax.servlet.http.HttpServlet;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import java.io.IOException;


   @WebServlet("/loginServlet")
   public class LoginServlet extends HttpServlet {

   	@Override
   	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   		//1.设置编码
   		req.setCharacterEncoding("utf-8");
   		//2.获取请求参数
   		String username = req.getParameter("username");
   		String password = req.getParameter("password");
   		//3.封装user对象
   		User loginUser = new User();
   		loginUser.setUsername(username);
   		loginUser.setPassword(password);

   		//4.调用UserDao的login方法
   		UserDao dao = new UserDao();
   		User user = dao.login(loginUser);

   		//5.判断user
   		if(user == null){
   			//登录失败
   			req.getRequestDispatcher("/failServlet").forward(req,resp);
   		}else{
   			//登录成功
   			//存储数据
   			req.setAttribute("user",user);
   			//转发
   			req.getRequestDispatcher("/successServlet").forward(req,resp);
   		}

   	}

   	@Override
   	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   		this.doGet(req,resp);
   	}
   }
   ```
7. 编写FailServlet和SuccessServlet类
   ```java
   @WebServlet("/successServlet")
   public class SuccessServlet extends HttpServlet {
   	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		//获取request域中共享的user对象
   		User user = (User) request.getAttribute("user");

   		if(user != null){
   			//给页面写一句话

   			//设置编码
   			response.setContentType("text/html;charset=utf-8");
   			//输出
   			response.getWriter().write("登录成功！"+user.getUsername()+",欢迎您");
   		}
   	}		
   ```

   ```java
   @WebServlet("/failServlet")
   public class FailServlet extends HttpServlet {
   	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		//给页面写一句话

   		//设置编码
   		response.setContentType("text/html;charset=utf-8");
   		//输出
   		response.getWriter().write("登录失败，用户名或密码错误");
   	}

   	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		this.doPost(request,response);
   	}
   }
   ```

8. `login.html`中form表单的action路径的写法
   虚拟目录+Servlet的资源路径

9. BeanUtils工具类，简化数据封装
   用于封装JavaBean的
   1. JavaBean：标准的Java类
      1. 要求：
         1. 类必须被public修饰
         2. 必须提供空参的构造器
         3. 成员变量必须使用private修饰
         4. 提供公共setter和getter方法
      2. 功能：封装数据
   2. 概念：
      成员变量：
      属性：setter和getter方法截取后的产物
      	例如：getUsername() --> Username--> username
   3. 方法：
      1. `setProperty()`: 设置属性
      2. `getProperty()`: 获取属性
      3. `populate(Object obj , Map map)`:将map集合的键值对信息，封装到对应的JavaBean对象中

## 重点API

```java
// 获取请求方式
req.getMethod();
// 获取工程名
req.getContextPath();
// 获取URL
req.getRequestURL();
// 获取IP
req.getRemoteAddr();

// 获取请求头
req.getHeader("use-agent");

// 获取请求参数
req.getParameter("username");
req.getParameterValues("hobby");
req.getParameterMap();

// 解决post请求的中文乱码
req.setCharacterEncoding("utf-8");

// 封装Javabean
BeanUtils.populate(user,map);
// 操作域对象
req.setAttribute();
req.getAttribute();
// 转发
req.getRequestDispatcher().forward();
```

## 作业

注册页面

用户名
密码
姓名
手机号
邮箱
籍贯

点击注册按钮 将用户填写的表单提交到Servlet 然后调用Dao进行插入数据库
根据返回的结果  做页面的跳转
如果插入成功了跳转到登录页面
如果插入失败了跳转到一个失败的Servlet 并且显示"注册失败"

跳转到登录页面后完成登录业务逻辑