# request和response:(要求:会使用)

## RESPONSE:案例1-文件下载案例

- 技术分析:

​	response
	文件下载

- response:响应

​	作用:
		往浏览器写东西
	组成部分:
		响应行 响应头 响应体

### 操作响应行 

- 格式:

`协议/版本 状态码 状态码说明`

- 状态码:

```
1xx:已发送请求
2xx:已完成响应
	200:正常响应
3xx:还需浏览器进一步操作
	302:重定向 配合响应头:location
	304:读缓存
4xx:用户操作错误
	404:用户操作错误.
	405:访问的方法不存在
5xx:服务器错误
	500:内部异常
```

- 常用方法:

​	setStatus(int 状态码):针对于 1 2 3 
	了解 :
		sendError(int 状态码):针对于 4xx和5xx

### 操作响应头 

- 格式:key/value(value可以是多个值)
- 常用的方法:

```
setHeader(String key,String value):设置字符串形式的响应头(只能设置一次，再设置会覆盖掉前面的)
了解:setIntHeader(String key,int value):设值整形的响应头
了解:setDateHeader(String key,long value):设值时间的响应头	

addHeader(String key,String value):添加置字符串形式的响应头（之前设置过则追加,若没有设置过则设置）-- **常用**
了解:addIntHeader(String key,int value):添加整形的响应头
了解:addDateHeader(String key,long value):添加时间的响应头
```

- 常用的响应头:

​	location:重定向
	refresh:定时刷新
        **content-type:设置文件的mime类型,设置响应流的编码及告诉浏览器用什么编码打开**
	content-disposition:文件下载

- 重定向:	

​	方式1:
		★response.sendRedirect("/day10/loc2");（就是将下面两个步骤封装起来）
	方式2:
		response.setStatus(302);
		respooen.setHeader("location","/day10/loc2");			

- 定时刷新:

​	方案1:设置头 refresh 昨天做过
		respooen.setHeader("refresh","秒数;url=跳转的路径");
	方案2:http的meta标签
		<meta http-equiv="refresh" content="3;url=/day10/refresh2.html">

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <!-- http-equiv:响应头        content:响应头的内容-->
        <meta http-equiv="refresh" content="3;url=/day10/refresh2.html">
        <title>Insert title here</title>
    </head>
    <body>
        XXXX成功了,<span id="sid">3</span>秒之后跳转到refresh2.html上.
    </body>
    <script type="text/javascript">
        onload=function(){
            //设置定时器
            setInterval(changeS, 1000);
        }

        i=3;
        //时间变化
        function changeS(){
            //1.获取元素
            var obj=document.getElementById("sid");

            //2.操作元素的标签体
            obj.innerHTML=--i;
        }
    </script>
</html>
```

### 操作响应体:

​	页面上要展示的内容

- 常用方法:

​		Writer getWriter():字符流
		ServletOutputStream getOutputStream() :字节流
         **自己写的东西用字符流,其他一概用字节流.**

- 处理响应中文乱码:

​	方式1:★
		response.setContentType("text/html;charset=utf-8");
	方式2:理解
		response.setHeader("content-type", "text/html;charset=utf-8");

- 注意:

​	两个流互斥。当响应完成之后,服务器会判断一下流是否已经关闭,若没有关闭,服务器会帮我们关闭.(底层使用的缓冲流)

### 文件下载:

- 下载方式:

​	1.超链接下载
		<a href="/day10/download/day10.txt">下载 day10.txt</a>
              **若浏览器能解析该资源的mime类型,则打开;若不能解析（比如自己瞎写的）则下载;**
	2.编码下载 通过servlet完成（两个头，一个流）**直接下载**

```
<a href="/day10/download?name=day10.txt">下载 day10.txt</a>
a.设置文件的mime类型
	String mimeType=context.getMimeType(文件名)
	response.setContentType(mimeType);		
b.设置下载头信息 content-disposition
	response.setHeader("content-disposition", "attachment;filename="+文件名称);
c.提供流
	response.getOutputStream();
```

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //获取下载文件的名称
    String filename = request.getParameter("name");

    //文件下载
    //1.设置文件的mimeType
    String mimeType = context.getMimeType(filename);
    response.setContentType(mimeType);

    //2.设置下载的头信息
    response.setHeader("content-disposition", "attchment;filename="+filename);

    //3.对拷流
    //获取输入流
    InputStream is = context.getResourceAsStream("/download/"+filename);
    //获取输出流
    ServletOutputStream os = response.getOutputStream();

    int len=-1;
    byte[] b=new byte[1024];
    while((len=is.read(b))!=-1){
        os.write(b, 0, len);
    }

    IOUtils.copy(is, os);

    os.close();
    is.close();
}
```

- 扩展:使用commons-io工具类

​	对拷流:
		IOUtils.copy(is,os);

```java
/*int len=-1;
    byte[] b=new byte[1024];

    while((len=is.read(b))!=-1){
        os.write(b, 0, len);
    }
	*/
// 换成下面这个
IOUtils.copy(is, os);
```

- 扩展:

​	通过response生成验证码
	验证码:
		作用:防止暴力攻击
	点击换一张的js代码:

```
		function changeImg(obj){
			//操作src属性
			obj.src="/day10/code?i="+Math.random();
			//alert(1)
		}
```

## 案例2-完成用户注册操作

- 需求:

​	在一个表单页面上填写用户数据,点击提交,将所有的数据提交的服务器上,通过java代码最终保存到数据库中.

- 技术分析:

​	表单
	request

- request:请求

​	作用:获取浏览器发送过来的数据
	组成部分:
		请求行 请求头 请求体

### 操作请求行 

- 格式:

​    `请求方式 请求资源 协议/版本`

- 常用方法:HttpServletRequest

```
	掌握:
	String getMethod():获取请求方式
	String getRemoteAddr():获取ip地址
	String getContextPath() :在java中获取项目名称  (/day10)

	了解:
	getRequestURI():获取的是 从项目名到参数之前的内容  /day10/regist
	getRequestURL():获取的带协议的完整路径   http://localhost/day10/regist
	String getQueryString():get请求的所有参数   username=tom&password=123
	String getProtocol():获取协议和版本
	例如:请求行
		GET /day10/row?username=tom&password=123 HTTP/1.1	
```

### 操作请求头 

- 格式:key/value**(value可以是多个值)**
- 常用方法:

```
	★String getHeader(String key):通过key获取指定的value (一个)
	了解:
	    Enumeration getHeaders(String name) :通过key获取指定的value(多个)
	    Enumeration getHeaderNames() :获取所有的请求头的名称
	    int getIntHeader(String key):获取整型的请求头
	    long getDateHeader(String key):获取时间的请求头
	重要的请求头:
		user-agent:浏览器内核 msie firefox chrome
		referer:页面从那里来 防盗链
```

### 操作请求参数 ★

username=tom&password=123&hobby=drink&hobby=sleep

- 常用方法:

```
String getParameter(String key):获取一个值
String[] getParameterValues(String key):通过一个key获取多个值(上面hobby对应多个值)
Map<String,String[]> getParameterMap():获取所有的参数名称和值			

	username:tom
	---------values---------
	hobby:[drink, sleep]
	=====map=======
	username::[tom]
	password::[123]
	hobby::[drink, sleep]
```

- 请求的中文乱码:

1. 对于get请求:**参数追加到地址栏,会使用utf-8编码**,服务器(tomcat7)接受到请求之后,使用iso-8859-1解码,所以会出现乱码

2. 对于post请求,**参数是放在请求体中**,服务器获取请求体的时候使用iso-8859-1解码,也会出现乱码

   - 通用的方法（get,post都可以）:

   ​	解析参数后，用这个方式解码：new String(参数.getBytes("iso-8859-1"),"utf-8");

   ```java
   protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       //获取用户名和密码:
       String username = request.getParameter("username");
       String password = request.getParameter("password");
   	// 通用方法：
       username=new String(username.getBytes("iso8859-1"),"utf-8");
       System.out.println(username+"::"+password);
   }
   ```

   - 只针对于post请求来说:只需要将请求流的编码设置成utf-8即可

   ​		request.setCharacterEncoding("utf-8");

   ```java
   protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       //设置编码，post方法
       request.setCharacterEncoding("utf-8");
   
       //获取用户名和密码:
       String username = request.getParameter("username");
       String password = request.getParameter("password");
       
       System.out.println(username+"::"+password);
   }
   ```

### 扩展(编码解码):

​	URLEncoder.encode(s, "utf-8"); 指定编码
	URLDecoder.decode(s8, "iso8859-1");指定解码

- 文件下载扩展:

  ​	带中文名称的文件名下载的时候名称会出现问题

  - 常见的浏览器需要提供文件名称的utf-8编码
  - 对于火狐来说需要提供文件名称的base64编码

  ​	方案1:使用工具类
  	方案2:网上的方式(8成好使)
  		new String(filename.getByte("gbk"),"iso8859-1");

## 域对象request:

- request:

  ​	创建:一次请求来的时候
  	销毁:响应生成的时候
  	作用:
  		一次请求里面的数据
  	请求转发(请求链,请求串)

  `request.getRequestDispatcher("内部路径").forward(request,response);`

  其中的内部路径就是在web.xml中写的url-pattern

## 案例2-步骤分析:

```
1.数据库和表(day09)
2.页面(表单)
	给每个字段添加name属性
	还需修改表单提交的路径 method=post（这样就一句话解决乱码问题）
3.表单提交到一个servlet(RegistServlet)
4.RegistServlet:
	操作:
		接受数据,封装成一个user
		调用UserSerivce完成保存操作  int regist(User user)
		判断结果是否符合我们预期
			若int=1; 插入成功
			若int!=1; 插入失败
		相应的提示信息在当前的servlet不做处理,将信息转发给另一个servlet展示(MsgServlet);
5.UserService
	调用dao
6.userdao 通过dbutils在数据库中插入一条记录.
```

- 封装对象:

​	apache提供的一个工具类：BeanUtils封装数据
	使用步骤:
		1.导入jar包
		2.调用 BeanUtils.populate(Object bean,Map<> 参数);

## 请求转发和重定向区别:

重定向发送两次请求,请求转发一次请求

重定向地址栏发生该表,请求转发不变

重定向是从浏览器发送,请求转发是服务器内部

重定向不存在request域对象,请求转发可以使用request域对象

重定向是response的方法,请求转发是request的方法

重定向可以请求站外资源,请求转发不可以		