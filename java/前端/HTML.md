# HTML

注意看状态条

- Html是通过标签来定义的语言，代码都是由标签组成的
- html代码由<html></html> 组成。其中内容由头部分<head></head>和体部分<body></body>组成
- 头部分是给html页面增加一些辅助，属性信息。这里面的内容会最先加载
- 多数标签都有开始和结束标志，个别标签因为只有单一功能或者没有要修饰的内容可以在标签内结束。
- 想要对被标签修饰的内容进行操作，用到了标签的属性：<标签名  属性名=“属性值”> 数据内容 </标签名>
- html ：不严谨   ---->   xhtml：  ----> xml：要么有独立的闭合标签，要么在标签内部闭合

操作思想：为了操作数据，需要对数据进行不同标签的封装，通过标签中的属性对封装的数据进行操作，标签就相当于一个**容器**，对容器中的属性进行操作

- 转义符号&：< :  &lt    > :  &gt 。 空格也是特殊字符：&nbmp

- 注释：<!--   -->

- 列表：dl  (defined list) 

  ```
  <!--HTML注释：演示列表标签。
  		列表标签：dl
  		上层项目：dt
  		下层项目：dd : 封装的内容是会被缩进的。 有自动缩进效果。 
  -->
  ```

  ```
  <!--有序和无序的项目列表
  		有序：<ol>
  		无序：<ul>
  		无论有序和无序，条目的封装用的都是<li>
  		而且它们都有缩进效果。 		
  -->
  ```

- 图像：alt属性 ：光标放上去就能看到的文字--图片说明文字

  图像地图：图像中很多地方可以点一下与别的资源相关联，就叫热点区域

- 表格<table>：格式化数据用,

  1. 一行是<tr> , 一个单元格<td>, 加粗并居中的单元格<th>，
  2. <caption>表格标题</caption>
  3. <th colspan=2>个人信息</th>  让这一行占两列，否则下面一行有两列的话，他会只在左边一格。rowspan = 2 占两行
  4. 表格内部包括<thead>（一般是导航栏）,<tbody>（可有多个表格体）,<tfoot>
  5. <tbody><!--表格的下一级标签是tbody，不定义也存在--> 一个tbody接收完了，浏览器就会显示出这个页面，这样用户就可以看一个等下面的

# 超链接<a>

1. 作用：连接资源。
2. 当有了href属性才有了点击效果。
3. href属性的值的不同，解析的方式也不一样。
4. 如果在该值中没有指定过任何协议。解析时，是按照默认的协议来解析该值的。默认协议是file协议。即打开当前文件所在目录。

## 举例：

1. `<a href="imgs/1.jpg">美女图片</a>` ：这个连接就将本地图片显示出来
2. `<a>新浪网站</a>` 没有指定资源，会启动file引擎，打开本地文件目录
3. `<a href="mailto:abs@sina.com">联系我们</a>`  会打开本地邮件客户端
4. `<a href="www.sohu.com.cn">新浪网站</a>` 没有指定协议，被当做文件，所以按本地文件资源打开，所以会显示错误页面
5. `<a href="http://www.sohu.com.cn" target="_blank">新浪网站</a>`：启动了http引擎，用新窗口打开这个页面

```
//如果用上面的地址，浏览器能解析，就用默认下载器去下载
<a href="http://www.xunlei.com/movies/fczlm.rmvb">复仇者联盟</a><br/>
// 迅雷不高兴，想让用户下自己的软件，就写个自己的协议，让浏览器去找本机的迅雷软件
<a href="thunder://wertyuioasdfghjklwertyuio==">复仇者联盟</a>
```

6. `<a href="javascript:void(0)" onclick="alert('我弹')">这是一个超链接</a>` ：取消了超链接的默认点击效果，自定义效果，事件监听机制
7. 定位标记

```
<!--
定位标记。
专业术语：锚
-->
<a name=top>顶部位置</a>
<hr/>
<img src="111.jpg" height=900 width=400 border=10/>
<hr/>
<a name=center>中间位置</a>
<hr/>

<img src="111.jpg" height=900 width=400 border=10/>
<a href="#top">回到顶部位置</a>
<a href="#center">回到中间位置</a>
```

这样就可以回到本网页的某个位置

# 框架<frameset>

一个窗体显示多个资源。

主页面：

```html
<!--定义框架。使用标签frameset-->
	<frameset rows="30%,*">
		<frame src="top.html" name="top" />
		<frameset cols="30%,*">
			<frame src="left.html" name="left" />
			<frame src="right.html" name="right"/>
		</frameset>		
	</frameset>
```

left.html : 在左边边框定义了超链接，打开后显示在右边边框里

```html
<body>
    <H3>左边栏连接</H3>
    <a href="../img.html" target="right">链接一</a>
    <a href="../table.html"  target="right">链接一</a>
    <a href="../link.html" target="right">链接一</a>
</body>
```

## 画中画<iframe>

在窗体的任意位置上打开一个连接并访问内容。很多情况设置窗体大小为0，但是却在访问数据，会被恶意利用。

```html
<body>
    <!--iframe src="http://www.xxx.com/1.js" height=0 width=0>这是画中画标签，您如果看到该文字，很遗憾，您的浏览器不支持该标签</iframe-->

    <!--a href="a">下载地址1</a>
<a href="a">下载地址2</a-->

    <a href="sadfsdfad"><img src="c:\2.bmp" border=0/></a>
</body>
```

# 表单

```html
<body>
    <!--如果要给服务端提交数据，表单中的组件必须有name和value属性。
用于给服务端获取数据方便。-->
    <form>
        输入名称：<input type="text" name="user" value="" /><br/>
        <!--这样密码就会被隐藏-->
        输入密码：<input type="password" name="psw"  /><br/>	
        <!--两个单选框要放在一个组里面才能实现在两个框中只选一个，同一个组的设置：有相同的name属性即可。checked是默认选好-->
        选择性别：<input type="radio" name="sex" value="nan" />男
        <input type="radio" name="sex" value="nv" checked="checked" />女<br/>
        选择技术：<input type="checkbox" name="tech" value="java"/>JAVA
        <input type="checkbox" name="tech" value="html"/>HTML
        <input type="checkbox" name="tech" value="css"/>CSS<BR/>
        选择文件：<input type="file" name="file"/><br/>
        <!--图片组件具有提交效果（自带时间），点一下就会提交了-->		 
        一个图片：<input type="image" src="11.jpg"/><br/>
        <!--开发常用：算一些数据，数据不需要客户端知道，但是可以将其提交服务端。-->
        隐藏组件：<input type="hidden" name="myke" value="myvalue"/><br/>
        <!--没有自带时间，写上onclick是点击时间-->	
        一个按钮：<input type="button" value="有个按钮" onclick="alert('有个阿牛')" /><br/>


        <!--下拉菜单-->
        <select name="country">
            <option value="none">--选择国家--</option>
            <option value="usa">美国</option>
            <option value="en">英国</option>
            <!--默认选择的是中国-->
            <option value="cn" selected="selected">中国</option>
        </select>

        <textarea name="text"></textarea>
        <br/>
        <!--reset:将表单中数据进行状态还原，submit：提交给服务端，如果没有服务端就返回给当前页面。表单信息要提交给服务器，表单需要设置属性，这样服务端才能知道你传的数据对应的什么-->
        <input type="reset" value="清除数据"/><input type="submit" value="提交数据" />
    </form>
```

# Get和Post

## GET提交和POST提交的区别？

1,
get提交，提交的信息都显示在地址栏中。
post提交，提交的信息不显示地址栏中。

2，
get提交，对于敏感的数据信息不安全。
post提交，对于敏感信息安全。

3，
get提交，对于大数据不行，因为地址栏存储体积有限。
post提交，可以提交大体积数据。 

4，
get提交，将信息封装到了请求消息的请求行中。
post提交，将信息封装到了请求体中。

## 在服务端的一个区别。

- 如果出现将中文提交到tomcat服务器，服务器默认会用iso8859-1进行解码会出现乱码，

1. 通过iso8859-1进行编码，在用指定的中文码表解码。即可。这种方式对get提交和post提交都有效。 
2. 但是对于post提交方式提交的中文，还有另一种解决办法，就是直接使用服务端一个对象
3. request对象的setCharacterEncoding方法直接设置指定的中文码表就可以将中文数据解析出来。这个方法只对请求体中的数据进行解码。 
4. 综上所述：表单提交，建议使用post。

## 和服务端交互的三种方式：

1，地址栏输入url地址。get
2，超链接。 get
3，表单。 get 和  post

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=GBK">
        <!--meta http-equiv="refresh" content="3;url=http://www.sina.com.cn" /-->
        <title>Untitled Document</title>
    </head>
    <body>
        <!--
提交方式：get提交。
地址栏：http://10.1.31.69:9090/?user=abc&psw=123&repsw=123&sex=nan&tech=java&tech=html&country=cn

GET /?user=abc&psw=123&repsw=123&sex=nan&tech=java&tech=html&country=cn HTTP/1.1
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/x-shockwave-flash, application/vnd.ms-excel, application/vnd.ms-powerpoint, application/msword, */*
Accept-Language: zh-cn,zu;q=0.5
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.2)
Host: 10.1.31.69:9090
Connection: Keep-Alive


提交方式：POST
地址栏：http://10.1.31.69:9090/

POST / HTTP/1.1
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/x-shockwave-flash, application/vnd.ms-excel, application/vnd.ms-powerpoint, application/msword, */*
Accept-Language: zh-cn,zu;q=0.5
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.2)
Host: 10.1.31.69:9090
Content-Length: 68
Connection: Keep-Alive
Cache-Control: no-cache


-->
        <form action="http://10.1.31.69:9090" method="post">
            <table border="1" bordercolor="#0000ff" cellpadding=10 cellspacing=0 width=600>
                <tr>
                    <th colspan="2">注册表单</th>
                </tr>				
                <tr>
                    <td>用户名称：</td>
                    <td><input type="text" name="user" /></td>
                </tr>				
                <tr>
                    <td>输入密码：</td>
                    <td><input type="password" name="psw" /></td>
                </tr>				
                <tr>
                    <td>确认密码：</td>
                    <td><input type="password" name="repsw" /></td>
                </tr>				
                <tr>
                    <td>选择性别：</td>
                    <td>
                        <input type="radio" name="sex" value="nan" />男 
                        <input type="radio" name="sex" value="nv" />女
                    </td>
                </tr>				
                <tr>
                    <td>选择技术：</td>
                    <td>
                        <input type="checkbox" name="tech" value="java" />JAVA
                        <input type="checkbox" name="tech" value="html" />HTML
                        <input type="checkbox" name="tech" value="css" />CSS
                    </td>
                </tr>				
                <tr>
                    <td>选择国家：</td>
                    <td>
                        <select name="country">
                            <option value="none">--选择国家--</option>
                            <option value="usa">--美国--</option>
                            <option value="en">--英国--</option>
                            <option value="cn">--中国--</option>
                        </select>
                    </td>
                </tr>				
                <tr>
                    <th colspan="2">
                        <input type="reset" value="清除数据" />
                        <input type="submit" value="提交数据" />
                    </th>
                </tr>				
            </table>
        </form>
    </body>
</html>
```

## 校验

如果在客户端进行增强型的校验(只要有一个组件内容是错误，是无法继续提交的。只有全对才可以提交)

1：问，服务端数据后，还需要校验吗？

需要，为了安全性。比如直接在浏览器提交下面这样的数据，服务器也能接收。所以只需要记住字段的name和value属性范围即可。

user=hahah&psw=8989&repsw=8989&sex=nv&tech=html&tech=css&country=usa

2：如果服务端做了增强型的校验，客户端还需要校验吗？

比如：如果客户端发了错误的数据，发给服务器，服务器还要检验再返回。

需要，因为要提高用户的上网体验效果，减轻服务器端的压力。

# 头标签

<base>：href：网页中所有超链接的目录。targer ：打开方式 _blank是新窗口打开

<meta> ：网页的描述信息

<div>：没有具体含义，只为封装数据，封装整行

<p>：段落，前后有空行

<span>：封装

标签分为两大类。
1,块级标签(元素):标签结束后都有换行。div p dl table title ol ul 
2,行内标签(元素):标签结束后没有换行。 font span img input select a







