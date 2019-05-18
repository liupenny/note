# listener&filter

# listener(了解) 监听器

- 作用:

​		监听web中中的域对象 ServletContext ServletRequest HttpSession

## 监听内容:

注意:listener全部是接口（规范，自己去实现）

1. 监听三个对象的创建和销毁

​	ServletContextListener
	ServletRequestListener
	HttpSessionListener

2. 监听三个对象属性的变化

​	ServletContextAttributeListener
	ServletRequestAttributeListener
	HttpSessionAttributeListener

3. 监听session中javabean的状态

​	HttpSessionActivationListener(监听javabean是否钝化和活化)。钝化：将javabean从session中写到磁盘上
	HttpSessionBindingListener(绑定和解绑)绑定：将对象放在session中

- 使用步骤:

1. 编写一个类 实现接口
2. 重写方法
3. 编写配置文件(大部分都是)

## 演示各个监听器

1. 监听三个对象的创建和销毁

```
ServletContextListener
    创建:服务器启动的时候,会为每一个项目都创建一个servletContext
    销毁:服务器关闭的时候,或者项目被移除的时候
    以后用来在项目启动的时候加载配置文件(在spring里面就写一个classloaderListener,用来加载配置文件)
    
ServletRequestListener
    创建:请求来的时候
    销毁:响应生成的时候

HttpSessionListener
    创建:
        java中第一次调用request.getSession的时候(一个浏览器只有一个session，所以一旦创建后再次刷新也不会产生新的)
        jsp页面中访问的时候创建
    销毁:
        三种情况:
            session超时
            手动销毁session:session.invalidate()
            服务器非正常关闭
```

2. 监听三个对象属性的变化(添加 替换 删除)

```
ServletContextAttributeListener
ServletRequestAttributeListener
HttpSessionAttributeListener
```

3. 监听session中javabean的状态

​	注意:这两个接口需要javabean实现.是让javabean感知到自己的状态

		HttpSessionBindingListener(绑定valueBound和解绑valueUnbound方法)
			检测java是否添加到session或者从session中移除
		HttpSessionActivationListener(钝化和活化)
			钝化(sessionWillPassivate):javabean从session中序列化（必须实现序列化接口(只需要写一个:实现Serializable就行)才能写）到磁盘上。当服务器非正常关闭时，会把对象写入磁盘，当项目启动后，会自动把磁盘中session的内容取出。
			活化（sessionDidActivate）:javabean从磁盘上加载到了session中
			可以通过配置文件(tomcat的context.xml)修改javabean什么时候钝化(服务器如果同时将很多人的session保存在内存中很浪费，可以将session中暂时不用的对象钝化在内存中)
				修改一个项目
					只需要在项目下/meta-info创建一个context.xml
					内容如下:
						<Context>
							<!--
								maxIdleSwap	:1分钟 如果session不使用就会序列化到硬盘.
								directory	:itheima 序列化到硬盘的文件存放的位置.
							-->
							<Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
								<Store className="org.apache.catalina.session.FileStore" directory="itheima"/>
							</Manager>
						</Context>
# filter(★)	过滤器

- filter:过滤器

​	过滤请求和响应

- 作用:

```
    自动登录.
    统一编码.
    过滤关键字
    压缩响应信息
    ....
Filter是一个接口
```

- 编写filter步骤:

```
1.编写一个类
    a.实现filter接口
    b.重写方法
2.编写配置文件
    a.注册filter
    b.绑定路径
3.测试
```

```java
public class HelloFilter implements Filter{
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException {
        System.out.println("过滤器 hello filter 收到了请求~~~");

        //放行，才能到demo1
        chain.doFilter(request, response);

        //demo1执行完后回到这
        System.out.println("过滤器 hello filte 收到了响应~~~");
    }

    @Override
    public void destroy() {
    }
}
```



- Filter接口的方法(java ee的api):

1. init(FilterConfig config):初始化操作
2. doFilter(ServletRequest request, ServletResponse response, FilterChain chain):处理业务逻辑
3. destroy() :销毁操作

- filter的生命周期(了解)

1. filter单实例多线程
2. filter在服务器启动的时候 服务器创建filter 调用init方法 实现初始化操作
3. 请求来的时候,创建一个线程 根据路径调用dofilter 执行业务逻辑
4. 当filter被移除的时候或者服务器正常关闭的时候 调用destory方法 执行销毁操作.

- FilterChain:过滤链

​	通过**chain的dofilter方法**,可以将请求放行到下一个过滤器,直到最后一个过滤器放行才可以访问到servlet|jsp
	doFilter()放行方法

## ★url-pattern配置

​	3种

1. 完全匹配	必须以"/" 开始  例如: /a/b
2. 目录匹配	必须以"/" 开始 以"*"结束  例如:/a/b/*
3. 后缀名匹配	以"*."开始 以字符结束   例如 :  *.jsp  *.do  *.action

- 例如:

1. afilter  路径  /*
2. bFilter  路径  /demo4

- ★一个资源有可能被多个过滤器匹配成功,多个过滤器都会执行，且**执行顺序**是按照web.xml中filter-mapping的顺序执行的

# 案例1-自动登录

- 需求:

​	登录的时候,勾选自动登录,登录成功之后,关闭浏览器,下一次访问网站的时候完成登录操作(自动登录).

- 技术分析:

​	filter
	cookie

- 步骤分析:

1. 数据库和表

2. web项目

​		jar包 工具类 配置文件

3. 新建一个登录页面 表单
4. 表单提交 loginservlet

```
接受用户名和密码
    调用service完成登录操作,返回值User
    判断user是否为空
        若不为空,将user放入session中
            判断是否勾选了自动登录
                若勾选了:
                    需要将用户名和密码写回浏览器
```

5. 下次访问网站的时候

```
过滤器拦截任意请求(访问任何页面都要先登录，路径设置为/*)
    判断有无指定的cookie
        有cookie,获取用户名和密码
        调用service完成登录操作,返回user
        当user不为空的时候将user放入session中.
```

6. 两个问题

   - 当我们换用jack登录的时候发现登录不了.只能勾选了自动登录才行。因为访问所有页面都要从cookie中取值登录，即登录了多次。所以

   ​	自动登录只需要登录一次:即当session中没有用户的时候

   - 访问有些资源(看似没有登录，其实filter已经登录了)是不需要自动登录的(和登录还有注册相关的资源)
   - 修改filter的逻辑:

   ​	首先判断session中是否有user
   		若没有 并且访问的路径不是和登录注册相关的时候
   			才去获取指定的cookie

```java
public class AutoLoginFilter implements Filter{
    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
        throws IOException, ServletException {
        //1.强转
        HttpServletRequest request =(HttpServletRequest) req;
        HttpServletResponse response =(HttpServletResponse) resp;

        //2.完成自动登录
        //2.1 判断session中有无登录登录用户 没有的话继续自动登录
        User user=(User) request.getSession().getAttribute("user");
        if(user==null){
            //没有用户  需要自动登录
            //2.2 判断访问的资源是否和登录注册相关,若相关则不需要自动登录
            String path = request.getRequestURI();// /day1601/xxx
            if(!path.contains("/login")){

                //2.3获取指定的cookie
                Cookie c = CookUtils.getCookieByName("autologin", request.getCookies());
                //判断cookie是否为空
                //若不为空 获取值(username和passowrd) 调用serivce完成登录  判断user是否为空 不为空 放入session
                if(c!=null){
                    String username=c.getValue().split("-")[0];
                    String password=c.getValue().split("-")[1];

                    //调用serivce完成登录
                    //user=null;
                    try {
                        user = new UserService().login(username, password);
                    } catch (SQLException e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                    }

                    if(user!=null){
                        //将user放入session中
                        request.getSession().setAttribute("user", user);
                    }
                }
            }
        }

        //3.放行
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException 	{
    }

    @Override
    public void destroy() {
    }
}
```

## 扩展：记住用户名

```jsp
<body>
    <form method="post" action="${pageContext.request.contextPath }/login">
        <table>
            <tr>
                <td>用户名</td>
                <td><input type="text" name="username" ></td>
            </tr>
            <tr>
                <td>密码</td>
                <td><input type="text" name="password"></td>
            </tr>
            <tr>
                <td colspan="1"><input type="checkbox" name="saveName" value="ok">记住用户名</td>
                <td colspan="1"><input type="checkbox" name="autoLogin" value="ok">自动登录</td>
            </tr>
            <tr>
                <td colspan="2"><input type="submit"></td>
            </tr>

        </table>
    </form>
    <script type="text/javascript">
        onload=function(){
            var s="${cookie.savename.value }";
            //因为cookie不能存中文，所以先编码再解码
            s=decodeURI(s);
            alert(s);
            //将解码后的用户名付给username的文本框
            document.getElementsByName("username")[0].value=s;
        }
    </script>
</body>
```

- login.servlet。注意中文编码`Cookie c=new Cookie("savename", URLEncoder.encode(username, "utf-8"));`

```java
public class LoginServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //0.设置编码
        request.setCharacterEncoding("utf-8");

        //1.获取用户名和密码
        String username=request.getParameter("username");
        String password=request.getParameter("password");

        //2.调用service
        User user=null;
        try {
            user = new UserService().login(username,password);
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

        //3.判断user是否为空
        if(user==null){
            request.setAttribute("msg", "用户名和密码不匹配");
            request.getRequestDispatcher("/login.jsp").forward(request, response);
            return;
        }else{
            //若不为空 跳转到success.jsp
            request.getSession().setAttribute("user", user);
            //判断是否勾选了自动登录  若勾选了需要将用户名和密码放入cookie中, 写回浏览器
 if(Constant.IS_AUTO_LOGIN.equals(request.getParameter("autoLogin"))){
                //创建cookie 注意中文
                Cookie c=new Cookie("autologin", username+"-"+password);
                c.setMaxAge(3600);
                c.setPath(request.getContextPath()+"/");

                response.addCookie(c);
            }

            //判断是否勾选了记住用户名 若勾选了需要将用户名放入cookie中 写回浏览器
  if(Constant.IS_SAVE_NAME.equals(request.getParameter("saveName"))){
                //创建cookie,因为有中文所以先编码
                Cookie c=new Cookie("savename", URLEncoder.encode(username, "utf-8"));
                c.setMaxAge(3600);
                c.setPath(request.getContextPath()+"/");

                response.addCookie(c);
            }
            //页面重定向
 response.sendRedirect(request.getContextPath()+"/success.jsp");
        }
    }
```

# filter总结

- filterConfig:(了解)

```
过滤器的配置对象
作用:
    获取全局管理者
    获取当前filter的名称
    获取当前filter的初始化参数
```

- filter-mapping的子标签(理解)(url-pattern,servlet-name二者任意出现一个)

```
servlet-name:匹配那个servlet 值写的是serlvet标签中servlet-name的值（具体拦截哪几个，就写哪几个）
建议:不要在一个filter中重复的匹配servlet
    例如: serlvet的url-pattern为  /a/b/hello   serlvetname:HelloServlet
        如果filter中的url-pattern  /*
        最好不要在写 servlet-name:HelloServlet
```

- dispatcher(可以出现任意次):

​	表示filter匹配哪种请求。默认的是REQUEST（即默认只连接浏览器转发过来的请求，在servlet内部又访问不去连接了）,一旦显式的写出来哪种请求,默认就不起作用了

```
理解
    REQUEST:从浏览器发送过来的请求(默认) 理解
    FORWARD:转发过来的请求 理解
了解
    ERROR:因服务器错误而发送过来的请求
    <error-page>(web.xml里面有)
    <error-code>404</error-code>
    <location>/404.jsp</location>
  	</error-page>
    
    INCLUDE:包含过来的请求
```

# 案例2-统一字符编码

- 需求:

​	以前我们开发的时候若有参数,第一步都是设置编码,才不会出现乱码,通过过滤器设置(重写request的getParameter的方法),到servlet或者jsp上的时候已经没有乱码问题

- 技术分析:

1. filter 配置**路径/*(所有页面)** 过滤器的**第一个位置**
2. 在filter中重写getParameter(加强)

- 步骤分析:

​	我们只需要在filter中 对request进行加强(例如:只对request.getParameter()进行加强)

	方法加强:
		1.继承(获取构造器)
		2.装饰者模式(静态代理)，继承这个类httpServletRequestWrapper即可，他已经帮我们包装好了
		3.动态代理
		
	装饰者书写步骤:
		1.要求装饰者和被装饰者实现同一个接口或者继承同一个类
		2.装饰者中要有被装饰者的引用
		3.对需要加强方法进行加强
		4.对不需要加强的方法调用原来的方法即可
		
	加强request.getParameter(String key)
		首先请求的方式不同,处理的方式也不同
			获取请求的方法
			若是get请求
				new String(value.getBytes("iso8859-1"),"utf-8");
			若是post请求
				只需要设置一句话
				request.setCharacterEncoding("utf-8");
				
	最后将包装过的request对象(MyRequest)传递给servlet即可
```java
public class EncodingFilter implements Filter{
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // TODO Auto-generated method stub
    }

    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
        throws IOException, ServletException {
        //1.强转
        HttpServletRequest request=(HttpServletRequest) req;
        HttpServletResponse response=(HttpServletResponse) resp;

        //2.放行 将包装过的request传递
        chain.doFilter(new MyRequest(request), response);
    }

    @Override
    public void destroy() {
        // TODO Auto-generated method stub

    }

}

class MyRequest extends HttpServletRequestWrapper{
    private HttpServletRequest request;

    public MyRequest(HttpServletRequest request) {
        super(request);
        this.request=request;
    }

    @Override
    public String getParameter(String name) {
        //获取请求方式
        String m = request.getMethod();
        if("get".equalsIgnoreCase(m)){
            //get请求
            String value = request.getParameter(name);
            try {
                return new String(value.getBytes("iso8859-1"),"utf-8");
            } catch (UnsupportedEncodingException e) {
            }
        }else if("post".equalsIgnoreCase(m)){
            try {
                request.setCharacterEncoding("utf-8");
            } catch (UnsupportedEncodingException e) {
            }
            return request.getParameter(name);

        }
        return super.getParameter(name);
    }
}
```

## 关于获取参数的方法

1. String getParameter(String name);// 相当于2的arr[0]
2. String[] getParameterValues(String name);// 相当于3的map.get(name)，map中没乱码了，那这里肯定没乱码
3. Map<String,String[]> getParameterMap(); 所以只需要对map请求进行编码即可。若是get请求，对数组中每个值new string即可（只能编一次码，代码中有flag设置）。post可多次编码。

