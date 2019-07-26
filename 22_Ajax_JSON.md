# Javaweb第19天笔记

## 今日内容

1. AJAX
2. JSON

## AJAX

### 概念： ASynchronous JavaScript And XML	异步的JavaScript 和 XML
Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术
通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。
提升用户的体验

使用Javascript技术发送异步请求 在和服务器交互时使用的数据格式是xml
异步请求  局部刷新
可以使用HTML+JavaScript向后台发送异步请求  从而达到和服务器交互的目的
实现方式:
1. 原生的JS实现  了解
2. 使用jQuery去实现 掌握
    
### 异步和同步：客户端和服务器端相互通信的基础上

* 客户端必须等待服务器端的响应。在等待的期间客户端不能做其他操作。
* 客户端不需要等待服务器端的响应。在服务器处理请求的过程中，客户端可以进行其他的操作。
* 同步与异步举例
   发信息与打电话：打电话一心一意打电话（不能同时进行其他操作）
   发信息：信息发送完毕，等待收到答复前这个时间段，进行其他操作
   ![](http://os4z8t7lb.bkt.clouddn.com/201803080819_928.png)

### 异步请求对象(XMLHttpRequest)

#### 属性:
`onreadystatechange` 指定当readyState属性改变时的事件处理句柄
`readyState`  返回当前请求的状态，只读. 0 1 2 3 4
            0: 请求未初始化
            1: 服务器连接已建立
            2: 请求已接收
            3: 请求处理中
            4: 请求已完成，且响应已就绪
`status` 返回当前请求的http状态码.只读
`statusText`  返回当前请求的响应行状态，只读
`responseXML` 将响应信息格式化为Xml Document对象并返回，只读

#### 方法:
open(请求方式,请求路径,是否异步) 创建一个新的http请求，并指定此请求的方法、URL以及验证信息
send(请求参数) 发送请求到http服务器并接收回应
setRequestHeader(头信息,头的值) 单独指定请求的某个http头,处理POSt请求方式中文问题

![](http://os4z8t7lb.bkt.clouddn.com/201801060819_301.png)

### AJAX的编写的步骤

* 创建一个异步对象
* 设置对象状态改变触发一个函数
* 设置向后台提交的路径
* 发送请求


#### 原生的JS实现方式（了解）

```javascript
function ajax_get() {
	// 1.创建异步对象
	var xhr = createXMLHttp();
	// 2.设置状态改变的监听 回调函数.
    xhr.onreadystatechange = function(){
        if(xhr.readyState == 4){ // 请求发送成功 接收数据成功
            if(xhr.status == 200){ // 响应也成功
                // 获得响应的数据:
                var data = xhr.responseText;
                // 将数据写入到DIV中:
                document.getElementById("d1").innerHTML = data;
            }
        }
    }
	// 3.设置请求路径
	xhr.open("GET","/day22/servletDemo?name=aaa&pass=123",true);
	// 4.发送请求
	xhr.send(null);
}

function createXMLHttp() {
    var xmlHttp;
    try { // Firefox, Opera 8.0+, Safari
        xmlHttp = new XMLHttpRequest();
    } catch (e) {
        try {// Internet Explorer
            xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
        } catch (e) {
            try {
                xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
            } catch (e) {
            }
        }
    }

    return xmlHttp;
}
```
        
#### JQeury实现方式

```JavaScript
$.post("/day22/ajaxServlet",{username:"rose"},function (data) {
        //解析数据
        alert(data);
 });
```


## JSON

### 概念： JavaScript Object Notation		JavaScript对象表示法

* json现在多用于存储和交换文本信息的语法
* 进行数据的传输
* JSON 比 XML 更小、更快，更易解析

### 语法：

1. 基本规则
    
  *  数据在名称/值对中：json数据是由键值对构成的
  * 键用引号(单双都行)引起来，也可以不使用引号
  * 值得取值类型：
     1. 数字（整数或浮点数）
     2. 字符串（在双引号中）
     3. 逻辑值（true 或 false）
     4. 数组（在方括号中）	`{"persons":[{},{}]}`
     5. 对象（在花括号中） `{"address":{"province"："陕西"....}}`
     6. null
  * 数据由逗号分隔：多个键值对由逗号分隔
  * 花括号保存对象：使用`{}`定义json 格式
  * 方括号保存数组：`[]`

2. 获取数据:
   1. `json对象.键名`
   2. `json对象["键名"]`
   3. `数组对象[索引]`
   4. 遍历

### JSON数据和Java对象的相互转换

常见的解析器：Jsonlib，Gson，fastjson，jackson

1. JSON转为Java对象
   1. 导入jackson的相关jar包
   2. 创建Jackson核心对象 ObjectMapper
   3. 调用ObjectMapper的相关方法进行转换 `readValue(json字符串数据,Class)`
2. Java对象转换JSON
   使用步骤：
    1. 导入jackson的相关jar包
    2. 创建Jackson核心对象 ObjectMapper
    3. 调用ObjectMapper的相关方法进行转换
        * `writeValue(参数1，obj)`
           参数1：
           File：将obj对象转换为JSON字符串，并保存到指定的文件中
           Writer：将obj对象转换为JSON字符串，并将json数据填充到字符输出流中
          OutputStream：将obj对象转换为JSON字符串，并将json数据填充到字节输出流中
        * `writeValueAsString(obj)` 将对象转为json字符串

3. 注解：
    * `@JsonIgnore`：排除属性。
    * `@JsonFormat`：属性值的格式化 `@JsonFormat(pattern = "yyyy-MM-dd")`

4. 复杂java对象转换
    1. List：数组
    2. Map：对象格式一致


# 案例

校验用户名是否存在
- 准备注册页面
- 给用户名输入框添加失去焦点事件
- 在事件函数中获取用户输入的用户名
- 发送异步请求到Servlet
- 在Servlet中接收用户名
- 调用Service-->调用Dao查询用户名是否被占用
- 将查询结果返回给Servlet
- 在Servlet中再返回给异步请求(`request.getWrite().print("内容");`)
- 在js代码中解析返回的数据(指定返回数据类型为json `type:"json"`)





