# Response课堂笔记

## 内容回顾

Servlet-->如果去写一个Servlet
HTTP协议-->无状态短连接
Request-->请求的封装
获取请求参数-->
请求转发-->
域对象-->
API-->
```java
/*
*  请求行 : 请求方式  请求资源路径  请求协议和版本
*  请求头
*  请求空行
*  请求体 get请求没有请求体  post请求体中放的是参数
* */
request.getMethod();
request.getRequestURI();
request.getRequestURL();
request.getHeader("use-agent");
request.getContextPath();

// 获取请求参数
request.getParameter("");
request.getParameterValues("");
request.getParameterMap();

// 请求转发
request.getRequestDispatcher("").forward(request,response);
// 域对象
request.setAttribute("msg","登录失败");
request.getAttribute("msg");
request.removeAttribute("msg");
```

## 今日内容

1. HTTP协议：响应消息
2. Response对象
3. ServletContext对象


## HTTP协议

### 请求消息：客户端发送给服务器端的数据
数据格式：
1. 请求行
2. 请求头
3. 请求空行
4. 请求体

### 响应消息：服务器端发送给客户端的数据

数据格式：
1. 响应行
	1. 组成：协议/版本 响应状态码 状态码描述
	2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态。
		1. 状态码都是3位数字 
		2. 分类：
			1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
			2. 2xx：成功。代表：200
			3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
			4. 4xx：客户端错误。
				* 代表：
					* 404（请求路径没有对应的资源） 
					* 405：请求方式没有对应的doXxx方法
			5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
				
	
2. 响应头：
	1. 格式：头名称： 值
	2. 常见的响应头：
		1. `Content-Type`：服务器告诉客户端本次响应体数据格式以及编码格式
		2. `Content-disposition`：服务器告诉客户端以什么格式打开响应体数据
			* `in-line`:默认值,在当前页面内打开
			* `attachment;filename=xxx`：以附件形式打开响应体。文件下载
3. 响应空行
4. 响应体:传输的数据


响应字符串格式
```html
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 101
Date: Wed, 06 Jun 2018 07:08:42 GMT

<html>
	<head>
	<title>$Title$</title>
	</head>
	<body>
	hello , response
	</body>
</html>
```

> 总结:
> 状态码: 是HTTP协议中规定的 来描述服务器向客户端浏览器响应数据是否成功的状态
> 在真实开发中 除了HTTP响应状态码 还会有自定义的状态码(跟自己产品响应的业务逻辑错误状态码)
> 后台开发会写接口文档  在文档的末尾一般都会附一个表 来说明自定义的状态码
> 200 302 304 404(访问资源不存在 路径错误) 500(服务器内部错误 Java代码挂了) 



## Response对象

功能：设置响应消息
1. 设置响应行
	1. 格式：HTTP/1.1 200 ok
	2. 设置状态码：`setStatus(int sc)`
   
2. 设置响应头：`setHeader(String name, String value)`  `addHeader()`;
	响应头一般是一对一的键值对  也有一对多的键值对  Key: v1,v2,v3,v4
	
3. 设置响应体：
   使用步骤：
	1. 获取输出流
		* 字符输出流：`PrintWriter getWriter()`
		* 字节输出流：`ServletOutputStream getOutputStream()`
	2. 使用输出流，将数据输出到客户端浏览器


### 重定向

重定向：资源跳转的方式
```java
//1. 设置状态码为302
response.setStatus(302);
//2.设置响应头location 
response.setHeader("location","/day15/responseDemo2");
//简单的重定向方法　重点API
response.sendRedirect("/day15/responseDemo2");

//页面跳转的其他方式
//页面：超链接  `location.href=""`
//代码: 转发   重定向
```

重定向的特点:redirect
1. 地址栏发生变化
2. 重定向可以访问其他站点(服务器)的资源
3. 重定向是两次请求。不能使用request对象来共享数据
转发的特点：forward
1. 转发地址栏路径不变
2. 转发只能访问当前服务器下的资源
3. 转发是一次请求，可以使用request对象来共享数据
	

路径写法：
1. 相对路径：通过相对路径不可以确定唯一资源
	* 如：`./index.html`
	* 不以`/`开头，以`.`开头路径

	* 规则：找到当前资源和目标资源之间的相对位置关系
		* `./`：当前目录
		* `../`:后退一级目录
2. 绝对路径：通过绝对路径可以确定唯一资源
	* 如：`http://localhost/day15/responseDemo2`  `/day15/responseDemo2`
	* 以`/`开头的路径

	* 规则：判断定义的路径是给谁用的？判断请求将来从哪儿发出
		* 给客户端浏览器使用：需要加虚拟目录(项目的访问路径)
			* 建议虚拟目录动态获取：`request.getContextPath()`
			* `<a>` , `<form>` 重定向...
		* 给服务器使用：不需要加虚拟目录
			* 转发路径

总结:
- HTML页面中写路径
  如果引用的是css  js  图片  建议使用相对路径
  如果引用的是Servlet建议使用绝对路径
	
- JSP页面中写路径
  不管引用的是什么资源　都建议使用绝对路径
	
- Servlet中写路径
  不管是什么资源　都建议写绝对路径
  除了：转发不能写工程名
		

绝对路径: `/工程名/资源路径(资源名称)`
服务器路径：　服务器内部资源跳转　，不需要加工程名　例子：请求转发
客户端路径：  从客户端发出来的请求路径  都要加工程名 
		
什么时候使用转发
什么时候使用重定向

在进行资源跳转之前 看有没有使用到request域对象 
如果使用了request域对象就必须使用转发
如果没有使用request域对象两个都可以使用  建议使用重定向(表单重复提交)  	
				
### 服务器输出字符数据到浏览器

字符输出流 `response.getWriter().writer("xxx");`
> 注意乱码问题：
  简单的形式，设置编码，是在获取流之前设置
  `response.setContentType("text/html;charset=utf-8");`

字节流 `response.getOutputStream();`
	


## ServletContext对象

1. 概念：代表整个web应用，可以和程序的容器(服务器)来通信
2. 获取：
	1. 通过request对象获取
		`request.getServletContext();`
	2. 通过HttpServlet获取
		`this.getServletContext();`
3. 功能：
	1. 获取MIME类型：
		* MIME类型:在互联网通信过程中定义的一种文件数据类型
			* 格式： 大类型/小类型   text/html		image/jpeg
		* 获取：`String getMimeType(String file)`
	2. 域对象：共享数据
		1. `setAttribute(String name,Object value)`
		2. `getAttribute(String name)`
		3. `removeAttribute(String name)`
		4. ServletContext对象范围：所有用户所有请求的数据
	3. 获取文件的真实(服务器)路径 `String getRealPath(String path)`  
       ```java
		`String b = context.getRealPath("/b.txt");`//web目录下资源访问
		System.out.println(b);

		String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INF目录下的资源访问
		System.out.println(c);

		String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
		System.out.println(a);
       ```
## 案例

* 文件下载需求：
	1. 页面显示超链接
	2. 点击超链接后弹出下载提示框
	3. 完成图片文件下载


* 分析：
	1. 超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能解析，则弹出下载提示框。不满足需求
	2. 任何资源都必须弹出下载提示框
	3. 使用响应头设置资源的打开方式：
		* content-disposition:attachment;filename=xxx


* 步骤：
	1. 定义页面，编辑超链接href属性，指向Servlet，传递资源名称filename
	2. 定义Servlet
		1. 获取文件名称
		2. 使用字节输入流加载文件进内存
		3. 指定response的响应头： content-disposition:attachment;filename=xxx
		4. 将数据写出到response输出流


* 问题：
	* 中文文件问题
		* 解决思路：
			1. 获取客户端使用的浏览器版本信息
			2. 根据不同的版本信息，设置filename的编码方式不同


## 作业

1. 先完成注册功能 
2. 注册成功跳转到登录页面 
3. 完成登录的功能
4. 在登录成功以后显示当前用户是第几个登录该网站的人并显示下载链接
5. 点击下载链接跳转到下载页面
6. 完成下载功能