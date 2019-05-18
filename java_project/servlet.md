# servlet

# http:

要求:掌握一些头信息
	超文本传输协议:
		规定数据的格式
	浏览器往服务器发送 ---- 请求
	服务器往浏览器回写 ---- 响应
	

## 请求:(request)

- 组成部分:

		请求行 请求头 请求体	

- 请求行:请求信息的第一行

		格式:请求方式	访问的资源 协议/版本
	例如:GET /day0801/1.html HTTP/1.1
	请求方式:get和post
		get会把参数放在url的后面 post不会
		get参数大小有限制,post请求却没有限制
		get请求没有请求体;post请求有请求体 请求参数放在请求体中

- 请求头:请求信息的第二行到空行结束

		格式:key/value (value可以是多个值)
	常见的请求头:
		Accept: text/html,image/bmp		--支持数据类型    text/html text/css text/javascript 大类型/小类型 mime类型
		Accept-Charset: ISO-8859-1	--字符集
		Accept-Encoding: gzip		--支持压缩
		Accept-Language:zh-cn 		--语言环境
		Host: www.itcast.cn:80		--访问主机
		If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT	  --缓存文件的最后修改时间
		Referer: http://www.itcast.com/index.jsp	 --来自哪个页面、防盗链
		User-Agent: Mozilla/4.0 (compatible; MSIE 5.5; Windows NT 5.0)
		Cookie
		Connection:Keep-Alive   	--链接状态

- 需要掌握的头信息:

			Referer， User-Agent ，Cookie ，If-Modified-Since

- 请求体:空行以下的内容

		只有post才有请求体  get请求参数 http://xxxx?username=tom&password=123
	格式:username=tom&password=123

## 响应:(response)

- 组成部分:

			响应行 响应头 响应体

- 响应行:响应信息的第一行

			格式:协议/版本 状态码 状态码说明
		例如:HTTP/1.1 200 OK
		状态码:
			200 正常响应成功
			302 重定向
			304 读缓存
			404 用户操作资源不存在
			500 服务器内部异常

- 响应头:从响应信息的第二行到空行结束

			格式:key/value(value可以是多个值)
		常见的头
			Location: http://www.it315.org/index.jsp 	--跳转方向 和302一起使用的
			Server:apache tomcat			--服务器型号
			Content-Encoding: gzip 			--数据压缩
			Content-Length: 80 			--数据长度
			Content-Language: zh-cn 		--语言环境
			Content-Type: text/html; charset=GB2312 		--数据类型
			Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT	--最后修改时间
			Refresh: 1;url=http://www.it315.org		--定时刷新
			Content-Disposition: attachment; filename=aaa.zip	--下载
			Set-Cookie:SS=Q0=5Lb_nQ; path=/search
			Expires: -1					--缓存
			Cache-Control: no-cache  			--缓存
			Pragma: no-cache   				--缓存
			Connection: Keep-Alive   			--连接

- 需要掌握的头信息

		Content-Type Location  Last-Modified Refresh Content-Disposition Set-Cookie

- 响应体:空行以下的内容

		页面上展示的内容

# 案例分析

- 需求：

		在页面上输入用户名和密码,提交到服务器上,服务器拿着用户名和密码去数据库中查找有无此用户
	若有用户,则提示登录成功
	若无此用户,则提示用户名密码不匹配

- 技术分析:

		表单：用户输入
	servlet：接收处理信息的资源
	请求(request)
	响应(response)

- 表单:

		收集用户数据
	所有的字段要想提交到服务器必须 有name属性
	提交的地址 action
	请求方式:post

## servlet:

​	动态的web开发技术,本质就是一个类,运行在服务器端的一个java小程序
	处理业务逻辑,生成动态web内容

- 编写一个servlet步骤:

   1.编写一个类
   	a.继承HttpServlet
   	b.重写doGet或者doPost方法
   2.编写配置文件(web-inf/web.xml)
   	a.注册servlet
   	b.绑定路径（让绑定servlet类和对应的路径）
   3.访问
   	http://主机:端口号/项目名/路径

- 接受参数:  格式:key=value

​	Sting value=request.getParameter("key")
	例如: http://localhost/day09/hello?username=tom
		request.getParameter("username")就可以获取tom值

- 回写内容:response

​	response.getWriter().print("success");
	处理响应数据中文乱码:
`resp.setContentType("text/html;charset=utf-8"); //建议大家放在方法中的第一行`

# serlvet总结:

servlet的体系结构:(了解)
	Servlet:接口
		|
	GenericServlet:抽象类
		|
	HttpServlet:抽象类
		|
	自定义servlet
	

	servlet常用方法:
		void init(ServletConfig config):初始化
		void service(ServletRequest request,ServletResponse response):服务 处理业务逻辑
		void destroy():销毁
		
		ServletConfig getServletConfig() :获取当前servlet的配置对象
	
	GenericServlet常用方法:	
		除了service方法没有显示,其他都实现了
		空参的Init() 若我们自己想对servlet进行初始化操作,重写这个init()方法即可
	
	HttpServlet常用方法：
		service做了实现，把参数强转，调用了重载的service方法
			重载的service方法获取请求的方式,根据请求方式的不同调用相应doXxx()方法
		doGet和doPost方法
	////////////////////////////////
## servlet生命周期 ★★★

```
void init(ServletConfig config):初始化
* 初始化方法
* 执行者:服务器
* 执行次数:一次
* 执行时机:默认第一次访问的时候
```

```
void service(ServletRequest request,ServletResponse response):服务 处理业务逻辑
* 服务
* 执行者:服务器
* 执行次数:请求一次执行一次
* 执行时机:请求来的时候	
```

```
void destroy():销毁
* 销毁
* 执行者:服务器
* 执行次数:只执行一次
* 执行时机:当servlet被移除的时候或者服务器正常关闭的时候
```

- serlvet是单实例多线程
- 默认第一次访问的时候,服务器创建servlet,并调用init实现初始化操作.并调用一次service方法
-  每当请求来的时候,服务器创建一个线程,调用service方法执行自己的业务逻辑
- **当serlvet被移除**的时候，或 **服务器正常关闭**的时候,服务器调用servlet的destroy方法实现销毁操作.

## url-pattern匹配

### **一、精确匹配**

`<url-pattern>`中配置的项必须与url完全精确匹配。

如配置信息如下：

```
<servlet-mapping>
    <servlet-name>MyServlet</servlet-name>
    <url-pattern>/kata/detail.html</url-pattern>
    <url-pattern>/demo.html</url-pattern>
    <url-pattern>/table</url-pattern>
</servlet-mapping>
```

当在浏览器中输入如下几种url时，都会被匹配到该servlet
http://10.43.11.143/myapp/kata/detail.html
http://10.43.11.143/myapp/demo.html
http://10.43.11.143/myapp/table

注意：

http://10.43.11.143/myapp/table/ 是非法的url，不会被当作http://10.43.11.143/myapp/table识别

另外上述url后面可以跟任意的查询条件，都会被匹配，如

http://10.43.11.143/myapp/table?hello 这个请求就会被匹配到MyServlet。

### **二、扩展名匹配**

如果匹配规则如下

```
<servlet-mapping>
    <servlet-name>MyServlet</servlet-name>
    <url-pattern>*.jsp</url-pattern>
</servlet-mapping>
```

则任何扩展名为jsp（文件名和路径任意）的url请求都会匹配，比如下面的url都会被匹配
http://10.43.11.143/myapp/demo.jsp
http://10.43.11.143/myapp/test.jsp

### **三、路径匹配**

如果匹配规则如下

```
<servlet-mapping>
    <servlet-name>MyServlet</servlet-name>
    <url-pattern>/kata/*</url-pattern>
</servlet-mapping>
```

则请求的ulr只要前面（myapp之后）的路径是/kata，而后面的路径可以任意。比如下面的url都会被匹配。
http://10.43.11.143/myapp/kata/demo.html
http://10.43.11.143/myapp/kata/test.jsp
http://10.43.11.143/myapp/kata/test/detail.html

http://10.43.11.143/myapp/kata/action

http://10.43.11.143/myapp/kata/action/

注意：路径和扩展名匹配无法同时设置，比如下面的三个`<url-pattern>`都是非法的，如果设置，启动tomcat服务器会报错。

```
<url-pattern>/kata/*.jsp</url-pattern>
<url-pattern>/*.jsp</url-pattern>
<url-pattern>he*.jsp</url-pattern>
```

另外注意：`<url-pattern>/aa/*/bb</url-pattern>`
这个是精确匹配，url必须是` /aa/*/bb`，这里的*不是通配的含义

### **四、匹配任意的url**

如果`<url-pattern>`配置成如下两种的任意一种

```
<url-pattern>/</url-pattern>
<url-pattern>/*</url-pattern>
```

则所有的url都可以被匹配上。其中/*是路径匹配，只是路径就是/。

### **五、优先顺序**

当一个url与多个servlet的匹配规则可以匹配时，则按照 “ 精确路径 > 最长路径>扩展名”这样的优先级匹配到对应的servlet。举例如下：

**例1：**比如servletA 的url-pattern为 /test，servletB的url-pattern为 /* ，这个时候，如果我访问的url为http://localhost/test ，这个时候容器就会先进行精确路径匹配，发现/test正好被servletA精确匹配，那么就去调用servletA，不会去管servletB。

**例2：**比如servletA的url-pattern为/test/*，而servletB的url-pattern为/test/a/*，此时访问http://localhost/test/a时，容器会选择路径最长的servlet来匹配，也就是这里的servletB。 

**例3: 比如**servletA的url-pattern：*.action ，servletB的url-pattern为 /* ，这个时候，如果我访问的url为http://localhost/test.action，这个时候容器就会优先进行路径匹配，而不是去匹配扩展名，这样就去调用servletB。

##  load-on-strap

- 在servlet标签(web.xml文件配置中)有一个子标签 load-on-startup

​	作用:用来修改servlet的初始化时机，对项目启动时进行初始化
	取值:正整数  值越大优先级越低

- tomcat的默认配置

1. 当访问的网页我们的配置文件里面没有指定配置的话,会查找tomcat的web.xml,
2. 即若请求我们自己的项目处理不了,tomcat的默认的servlet会帮我们处理信息

- 路径的写法:

​	相对路径:
		当前路径    ./ 或者 什么都不写
		上一级路径 ../
	绝对路径:(我们使用)
		带主机和协议的绝对路径(访问站外资源)
			http://www.itheima.com/xxxx
			http://localhost:80/day09/hello
		不带主机和协议的绝对路径（因为这个代表已经到了这个主页面）
			/day09/hello(经常使用)

# 案例

## 案例1-完成登录案例.

- 需求:

​	在页面上输入用户名和密码,提交到服务器上,服务器拿着用户名和密码去数据库中查找有无此用户
	若有用户,则提示登录成功
	若无此用户,则提示用户名密码不匹配

- 技术分析:

​	表单：用户输入
	servlet：接收处理信息的资源
	请求(request)
	响应(response)

- 表单:

​	收集用户数据
	所有的字段要想提交到服务器必须 有name属性
	提交的地址 action
	请求方式:post

## 案例2-当我们登录失败,提示"用户名密码不匹配",3秒以后跳转到登录页面

- 技术分析:

​	定时刷新

- 常见的响应头-refresh

​	响应头格式:
		refresh:秒数;url=跳转的路径
	设置响应头:
		response.setHeader(String key,String value);设置字符串形式的响应头
		response.addHeader(String key,String value);追加响应头, 若之前设置设置过这个头,则追加;若没有设置过,则设置
	设置定时刷新:
		response.setHeader("refresh","3;url=/day0901/login.htm");

- 步骤分析:

​	登录失败之后,修改业务逻辑。打印之后添加一个头信息即可

```java

```

## 案例3-统计登录成功的总人次,

- 需求:

​	在一个用户登录成功之后,获取之前登录成功总人次,将次数+1.在访问另一个servlet的时候,显示登录成功的总人次

- 技术分析:

​	ServletContext

- ServletContext:

​	上下文(全局管理者)
	常用的方法:
		setAttribute(String key,Object value);//设置值
		Object getAttribute(String key);//获取值
		removeAttribute(String key)://移除值
获取全局管理者:
	getServletContext():

- 步骤分析:

​	1.在项目启动的时候,初始化登录次数
	在loginservlet的init()方法中获取全局管理者,将值初始化为0,放入servletcontext上
	2.登录成功之后,在loginservlet中获取全局管理者,获取登录成功的总次数
	3.然后将次数+1,让后将值设置回去
	4.当访问showServlet的时候,获取全局管理者,获取登录成功的总次数,然后在页面上打印出来即可

## ServletConfig:(了解)

- servlet配置对象

​	作用:
		1.获取当前servlet的名称
		2.获取当前servlet的初始化参数
		3.获取全局管理者
	方法:

```
String getServletName():获取当前servlet的名称(web.xml配置的servlet-name)
String  getInitParameter(String key):通过名称获取指定的参数值
Enumeration getInitParameterNames() :获取所有的参数名称
初始化参数是放在 web.xml文件 
servlet标签下子标签 init-param	
```

- ★getServletContext():获取全局管理者

servletconfig是由服务器创建的,在创建servlet的同时也创建了它,通过servlet的init(ServletConfig config)将config对象传递给servlet,由servlet的getServletConfig方法获取

## ServletContext:理解

- 概念

​	上下文(全局管理者)
	一个项目的引用.代表了当前项目.
	当项目启动的时候,服务器为每一个web项目创建一个servletcontext对象.
	当项目被移除的时候或者服务器关闭的时候servletcontext销毁

- 作用:

​		1.获取全局的初始化参数
		2.servlet之间共享资源(xxxAttribute)
		3.获取文件资源
		4.其他操作

- 获取servletcontext:

​		方式1:了解 
			getServletConfig().getServletContext()
		方式2:
			getServletContext()

- 常用方法:

​	1.了解
		String  getInitParameter(String key):通过名称获取指定的参数值
		Enumeration getInitParameterNames() :获取所有的参数名称	
		在根标签下有一个 context-param子标签 用来存放初始化参数
				<context-param>
					<param-name>encoding</param-name>
					<param-value>utf-8</param-value>
				</context-param>
	2.xxxAttribute
	3.String getRealPath(String path):获取文件部署到tomcat上的真实路径(带tomcat路径)
		getRealPath("/"):D:\javaTools\apache-tomcat-7.0.52\webapps\day09\
	InputStream getResourceAsStream(String path):以流的形式返回一个文件
	4.获取文件的mime类型  大类型/小类型
		String getMimeType(String 文件名称)

## 域对象:★★★（servletcontext）

- 当成map集合
- 常用方法:

​	xxxAttribute() - set,get,remove

- servletcontext创建和销毁:

​	当项目启动的时候,服务器为每一个web项目创建一个servletcontext对象.
	当项目被移除的时候或者服务器关闭的时候servletcontext销毁

- 存放:

​	共享的数据

## 获取文件的路径:

- 通过类加载器获取文件:2.txt。源文件放在src目录下，在编译完成后会自动加载到classes目录下（无论是java项目还是web项目都可以）：

`类.class.getClassLoader().getResource("2.txt").getPath()`

`/F:/lab/java_practice/webprac/out/artifacts/webprac_war_exploded/WEB-INF/classes/1.txt`