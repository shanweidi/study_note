# Cookie&&Session

## 内容回顾

1. Servlet的生命周期
   当有用户第一次访问Servlet的时候就会调用该Servlet的`init()`方法进行初始化 该初始化方法只会被执行一次
   然后再有其他用户来访问这个Servlet的时候不会再进行初始化 会为每个用户单独开辟一个子线程来访问该Servlet的`Service()`方法
   当该项目关闭或者从服务器中移除或者是服务器关闭 该Servlet销毁  `destroy()`方法会被执行

2. Request的作用及其常用方法
   接收请求参数
   获取客户机的相关信息
   作为域对象存取数据
   用作转发

3. Response的作用及其常用方法
   可以给客户机响应消息
   可以重定向

4. 转发和重定向的区别
   转发地址栏不会发生变化  重定向会发生变化
   转发是一次请求一次响应 重定向是两次请求两次响应
   转发只能访问当前项目中的资源 重定向可以访问网络的任何资源
   转发路径不写工程名 重定向路径要写工程名
   转发可以使用request域对象 重定向不可以

5. 什么时候使用转发 什么时候使用重定向
   都可以做页面跳转
   做页面跳转推荐使用重定向
   如果在页面跳转之前往Request域对象中存储了数据 只能使用转发

6. ServletContext的作用及常用API
   获取全局初始化参数
   可以作为域对象存取数据
   获取资源的真实路径
   获取资源的输入流
   获取文件的mime类型

7. Request域对象和ServletContext域对象的作用范围
   Request域对象的作用范围是一次请求
   ServletContext域对象的范围是整个项目的生命周期

## 今日内容

1. 会话技术
   Cookie
   Session
2. JSP：入门学习

## 会话技术

HTTP协议: 无状态 短链接
会话跟踪技术: 四种
- Cookie :保存到浏览器客户端
- Session:保存到服务器内存中
- URL重写　　　`http://lcoalhost:8080/day15/downloadServlet?filename=1024.jpg`
- 隐藏表单域   `<input type="hidden" name="username">`

会话: 当用户访问到你的网站 会话开启 直到用户的浏览器关闭 该会话结束
一次会话中 可以有N多次的请求

1. 会话：一次会话中包含多次请求和响应。
   一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
2. Session功能：在一次会话的范围内的多次请求间，共享数据
3. 方式：
	1. 客户端会话技术：Cookie
	2. 服务器端会话技术：Session


> 总结:
> 会话在你发送第一个请求的时候就建立了 知道当前浏览器关闭  会话才结束
> 一次会话可以包含多次请求和响应
> 会话跟踪技术分类
> Cookie  Session  URL重写  隐藏表单域
> 会话可以记录保存用户的操作状态 Session可以保存一次会话之内的操作状态  Cookie可以保存多次会话之间的操作状态

## Cookie

1. 概念：客户端会话技术，将数据保存到客户端

2. 快速入门：
   使用步骤：
	1. 创建Cookie对象，绑定数据 `new Cookie(String name, String value)` 
	2. 发送Cookie对象 `response.addCookie(Cookie cookie)` 
	3. 获取Cookie，拿到数据 `Cookie[]  request.getCookies()`  

3. 实现原理
   基于响应头set-cookie和请求头cookie实现

4. cookie的细节
	4. 一次可不可以发送多个cookie?
		* 可以
		* 可以创建多个Cookie对象，使用response调用多次addCookie方法发送cookie即可。
	2. cookie在浏览器中保存多长时间？
		1. 默认情况下，当浏览器关闭后，Cookie数据被销毁
		2. 持久化存储：  `setMaxAge(int seconds)`
			1. 正数：将Cookie数据写到硬盘的文件中。持久化存储。并指定cookie存活时间，时间到后，cookie文件自动失效
			2. 负数：默认值
			3. 零：删除cookie信息
	3. cookie能不能存中文？
		* 在tomcat 8 之前 cookie中不能直接存储中文数据。
			* 需要将中文数据转码---一般采用URL编码(%E3)
		* 在tomcat 8 之后，cookie支持中文数据。特殊字符还是不支持，建议使用URL编码存储，URL解码解析
	4. cookie共享问题？
		1. 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享？
			* 默认情况下cookie不能共享
			* setPath(String path):设置cookie的获取范围。默认情况下，设置当前的虚拟目录
				* 如果要共享，则可以将path设置为"/"
		2. 不同的tomcat服务器间cookie共享问题？
			* setDomain(String path):如果设置一级域名相同，那么多个服务器之间cookie可以共享
				* setDomain(".baidu.com"),那么tieba.baidu.com和news.baidu.com中cookie可以共享
		
5. Cookie的特点和作用
	1. cookie存储数据在客户端浏览器
	2. 浏览器对于单个cookie 的大小有限制(4kb) 以及 对同一个域名下的总cookie数量也有限制(20个)

	* 作用：
		1. cookie一般用于存出少量的不太敏感的数据
		2. 在不登录的情况下，完成服务器对客户端的身份识别

6. 案例：记住上一次访问时间
	1. 需求：
		1. 访问一个Servlet，如果是第一次访问，则提示：您好，欢迎您首次访问。
		2. 如果不是第一次访问，则提示：欢迎回来，您上次访问时间为:显示时间字符串

	2. 分析：
		1. 可以采用Cookie来完成
		2. 在服务器中的Servlet判断是否有一个名为lastTime的cookie
			1. 有：不是第一次访问
				1. 响应数据：欢迎回来，您上次访问时间为:2018年6月10日11:50:20
				2. 写回Cookie：lastTime=2018年6月10日11:50:01
			2. 没有：是第一次访问
				1. 响应数据：您好，欢迎您首次访问
				2. 写回Cookie：lastTime=2018年6月10日11:50:01

	3. 代码实现：
	   ```java
		package cn.itcast.cookie;

		import javax.servlet.ServletException;
		import javax.servlet.annotation.WebServlet;
		import javax.servlet.http.Cookie;
		import javax.servlet.http.HttpServlet;
		import javax.servlet.http.HttpServletRequest;
		import javax.servlet.http.HttpServletResponse;
		import java.io.IOException;
		import java.net.URLDecoder;
		import java.net.URLEncoder;
		import java.text.SimpleDateFormat;
		import java.util.Date;


		@WebServlet("/cookieTest")
		public class CookieTest extends HttpServlet {
			protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
				//设置响应的消息体的数据格式以及编码
				response.setContentType("text/html;charset=utf-8");
		
				//1.获取所有Cookie
				Cookie[] cookies = request.getCookies();
				boolean flag = false;//没有cookie为lastTime
				//2.遍历cookie数组
				if(cookies != null && cookies.length > 0){
					for (Cookie cookie : cookies) {
						//3.获取cookie的名称
						String name = cookie.getName();
						//4.判断名称是否是：lastTime
						if("lastTime".equals(name)){
							//有该Cookie，不是第一次访问
		
							flag = true;//有lastTime的cookie
		
							//设置Cookie的value
							//获取当前时间的字符串，重新设置Cookie的值，重新发送cookie
							Date date  = new Date();
							SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
							String str_date = sdf.format(date);
							System.out.println("编码前："+str_date);
							//URL编码
							str_date = URLEncoder.encode(str_date,"utf-8");
							System.out.println("编码后："+str_date);
							cookie.setValue(str_date);
							//设置cookie的存活时间
							cookie.setMaxAge(60 * 60 * 24 * 30);//一个月
							response.addCookie(cookie);
		
		
							//响应数据
							//获取Cookie的value，时间
							String value = cookie.getValue();
							System.out.println("解码前："+value);
							//URL解码：
							value = URLDecoder.decode(value,"utf-8");
							System.out.println("解码后："+value);
							response.getWriter().write("<h1>欢迎回来，您上次访问时间为:"+value+"</h1>");
		
							break;
		
						}
					}
				}
		
		
				if(cookies == null || cookies.length == 0 || flag == false){
					//没有，第一次访问
		
					//设置Cookie的value
					//获取当前时间的字符串，重新设置Cookie的值，重新发送cookie
					Date date  = new Date();
					SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
					String str_date = sdf.format(date);
					System.out.println("编码前："+str_date);
					//URL编码
					str_date = URLEncoder.encode(str_date,"utf-8");
					System.out.println("编码后："+str_date);
		
					Cookie cookie = new Cookie("lastTime",str_date);
					//设置cookie的存活时间
					cookie.setMaxAge(60 * 60 * 24 * 30);//一个月
					response.addCookie(cookie);
		
					response.getWriter().write("<h1>您好，欢迎您首次访问</h1>");
				}
		
		
			}
		
			protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
				this.doPost(request, response);
			}
		}
       ```

Cookie总结:

Cookie是啥 保存数据的一种技术
能干啥 可以保存数据
数据保存到哪了  浏览器客户端
怎么保存  
得先搞一个`Cookie newCookie(String , String)` 
然后向浏览器添加  `response.addCookie(cookie)`
保存完以后怎么使用
`request.getCookies()`

Cookie的存活时间
Cookie的分类
1. 会话级别的Cookie (默认的) 
   `new Cookie(String,String)` 如果不设置`setMaxAge()` 默认就是会话级别的Cookie 浏览器关闭会话结束 Cookie自动销毁
   `setMaxAge()`的默认值是  -1
2. 持久级别的Cookie
   通过`setMaxAge()` 方法来设置Cookie的最大存活时间
   一个星期  `setMaxAge(60 * 60 *24 *7)`
   在存活时间内如果访问或者使用了该Cookie 计数从新开始
   删除Cookie
   `getCookies();` 遍历
   `cookie.setMaxAge(0);`
   `response.addCookie(cookie);`

首先Cookie中不建议存中文 但是可以存储
Tomcat8之后可以直接存储中文
Tomcat8之前要进行编码 用什么进行编码 都可以

如果cookie没有setPath() 默认的路径是当前Servlet的虚拟路径的最后一个`/`   `/user/cookieDemo5`
不同的path可以出现同名的Cookie  相同的path下面同名的Cookie会覆盖
Cookie的访问范围
`Cookie cc = new Cookie("name","zhangsan");`
`cc.setMaxAge();`
`cc.setPath("/day16/user");`
访问: `/day16/ServletDemo1` 可以
      `/day16/user/user2/ServletDemo1` 可以
建议:
在设置Cookie的时候一般都要手动的去设置有效路径  有效时间
删除的时候要指定有效路径


## JSP：入门学习

JSP = HTML + Java + JSP自身的一些标签 特殊的HTML
JSP的应用
前后端分离 前端人员做页面 可以使用Ajax技术异步的请求后台服务器
JSP本身就是一个Servlet
3个指令  6个动作标签  9个内置对象(***************)

1. 概念：
   Java Server Pages： java服务器端页面
	* 可以理解为：一个特殊的页面，其中既可以指定定义html标签，又可以定义java代码
	* 用于简化书写！！！


2. 原理
	* JSP本质上就是一个Servlet

3. JSP的脚本：JSP定义Java代码的方式
	1. `<%  代码 %>`：定义的java代码，在service方法中。service方法中可以定义什么，该脚本中就可以定义什么。
	2. `<%! 代码 %>`：定义的java代码，在jsp转换后的java类的成员位置。
	3. `<%= 代码 %>`：定义的java代码，会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么。

4. JSP的内置对象：
	* 在jsp页面中不需要获取和创建，可以直接使用的对象
	* jsp一共有9个内置对象。
	* 今天学习3个：
		* request
		* response
		* out：字符输出流对象。可以将数据输出到页面上。和response.getWriter()类似
			* response.getWriter()和out.write()的区别：
				* 在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区数据。
				* response.getWriter()数据输出永远在out.write()之前
			
5. 案例:改造Cookie案例

## Session：主菜

1. 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中。HttpSession
2. 快速入门：
	1. 获取HttpSession对象：
		`HttpSession session = request.getSession();`
	2. 使用HttpSession对象：
		`Object getAttribute(String name)`  
		`void setAttribute(String name, Object value)`
		`void removeAttribute(String name)`  

3. 原理
   Session的实现是依赖于Cookie的。


4. 细节：
	1. 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？
		* 默认情况下。不是。
		* 如果需要相同，则可以创建Cookie,键为JSESSIONID，设置最大存活时间，让cookie持久化保存。
		  `Cookie c = new Cookie("JSESSIONID",session.getId());`
          `c.setMaxAge(60*60);`
		  `response.addCookie(c);`

	2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？
		* 不是同一个，但是要确保数据不丢失。tomcat自动完成以下工作
			* session的钝化：
				* 在服务器正常关闭之前，将session对象系列化到硬盘上
			* session的活化：
				* 在服务器启动后，将session文件转化为内存中的session对象即可。
			
	3. session什么时候被销毁？
		1. 服务器非正常关闭
		2. session对象调用invalidate() 。
		3. session默认失效时间 30分钟 tomcat中web.xml
			选择性配置修改
			<session-config>
				<session-timeout>30</session-timeout>
			</session-config>
			浏览器关闭 Session销毁了吗
			没有  只是保存这个Session的id的cookie被销毁了 但是Session中服务器内存中依然存在 超过失效时间自动销毁

	5. session的特点
		1. session用于存储一次会话的多次请求的数据，存在服务器端
		2. session可以存储任意类型，任意大小的数据

	* session与Cookie的区别：
		1. session存储数据在服务器端，Cookie在客户端
		2. session没有数据大小限制，Cookie有
		3. session数据安全，Cookie相对于不安全
   
Session总结:

什么是Session 可以存取数据 域对象
request           作用范围是一次请求
servletContext    作用范围是整个项目的生命周期
session(********) 作用范围 一次会话(包含多次请求)
setAttribute()
getAttribute()
获取Session request.getSession();
Session的保存时依赖于cookie()

## 案例：验证码

1. 案例需求：
	1. 访问带有验证码的登录页面login.jsp
	2. 用户输入用户名，密码以及验证码。
		* 如果用户名和密码输入有误，跳转登录页面，提示:用户名或密码错误
		* 如果验证码输入有误，跳转登录页面，提示：验证码错误
		* 如果全部输入正确，则跳转到主页success.jsp，显示：用户名,欢迎您

2. 分析：