# java-web项目目录

## WEB-INF

![img](assets\webp) 

- WEB-INF是Java的WEB应用的安全目录，所谓安全就是客户端无法访问，只有服务端可以访问的目录。 

- 所以：

  1. 资源文件只能放在WebContent下面,如 CSS,JS,image等.放在WEB-INF下引用不了. 

  2. 页面放在WEB-INF目录下面,这样可以限制访问,提高安全性.如JSP,html 。放在WEB-INF目录下就可以避免客户端直接在地址栏上输入路径进行访问了。基于不同的功能 ，把JSP 放置在WEB-INF下的不同的目录中。

  3. 只能用转向方式来访问WEB-INF目录下的JSP,不用采用重定向的方式请求该目录里面的任何资源

  4. WEB-INF目录下文件访问资源文件时,可以忽略WEB-INF这一层目录.如main.jsp 要用css目录里的一个css文件.  `<link rel="stylesheet" type="text/css" href="css/comm201005faa3.css" />`这样就行了,从客户端的地址可以看出来服务器转向main.jsp就是在webroot下面.所以main.jsp和css目录可以讲是同一级目录.

  5. WEB-INF/oa目录下访问images目录.怎么办呢.`<img alt="" src="images/instpage.gif"></body>`还是这样 

  6. WEB-INF目录下的文件之间如何访问呢.如在main.jsp用`<a href="oa.do">`测试OA的路径`</a>`访问
        像main.jsp有10处链接到WEB-INF目录下的其它页面.那就得有10个转向Action.这个可以用DispatchAction类加参数专门处理转向工作.

       注:

     - 转向方式: forward

     ​      如struts-config文件中配置`<forward name="success" path="/WEB-INF/main.jsp" /> `或 在Action中写`request.getRequestDispatcher("/WEB-INF/main.jsp").forward(request, response);`都是服务器读取了该页面内容,并发送到客户端.客户端的地址不变.内容跳转了

     - 重定向方式: Redirect

     ​     如struts-config文件中配置`<forward name="success" path="/WEB-INF/main.jsp"redirect="true"/>或在`action中写`response.sendRedirect("/error.jsp");`重定向的含义就是服务器把地址发给客户端,让客户端去访问.这种办法显然针对WEB-INF目录是无用功.

## classpath

- web项目编译后会有一个classpath路径，编译后的class文件，资源文件，依赖文件等都会放在这个路径下。 
- web application的classpath包含 WEB-INF/lib下的所有jar包和WEB-INF/classes目录 
- 所以在spring的配置文件写路径的时候都写：classpath。

## com.taotao.mapper

spring-dao.xml配置文件可以这么写。因为taotao-manager-mapper编译完之后会被打包为jar包，放在classpath目录下。所以可以采用`com.taotao.mapper`的方式引用这个jar包

```
 <!-- 配置扫描包，加载mapper代理对象到spring容器 -->
  <!-- 自动搜索加载sqlSessionFactory -->
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.taotao.mapper"/>
  </bean>
```

