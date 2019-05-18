# ajax和jquery

# 案例1-使用原生的ajax判断用户名是否占用(了解)

- 需求:

​	当我们在注册页面上输入用户名之后,点击下一个地方,去数据库中查询有无该用户名,最后提示信息

- 技术分析:

​	ajax

## ajax

- 异步JavaScript与XML技术

1. AJAX 是一种用于创建快速动态网页的技术。
2. 通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
3. 传统的网页（不使用 AJAX）如果需要更新内容，必须重载整个网页页面。

### ajax入门程序:

- 步骤:

```
1.创建一个核心对象 XMLHttpRequest
2.编写一个回调函数
3.编写请求方式和请求的路径(open操作)
4.发送请求(send操作)
```

### ajax-api详解

- 常用属性:

```
onreadystatechange:检测readyState状态改变的时候
readyState:ajax核心对象的状态
    0:核心对象创建
    1:调用了open方法
    2:调用了send方法
    3:部分响应已经生成(没有意思)
    4:响应已经完成(使用的是这个状态)（不一定是正确的响应）
status:状态码(200是正常响应的状态)
    if(xmlhttp.readyState==4 && xmlhttp.status==200){
    }
responseText:响应回来的文本
```

- ajax入门：jsp（get方法）

```jsp
<script type="text/javascript">
    function btnClick(){
        //1.创建核心对象
        xmlhttp=null;
        if (window.XMLHttpRequest)
        {// code for Firefox, Opera, IE7, etc.
            xmlhttp=new XMLHttpRequest();
        }
        else if (window.ActiveXObject)
        {// code for IE6, IE5
            xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
        }

        //2.编写回调函数
        xmlhttp.onreadystatechange=function(){
            //alert(xmlhttp.readyState);
            if(xmlhttp.readyState==4 && xmlhttp.status==200){
                //alert('ok');
                //接受服务器回送过来的数据
                alert(xmlhttp.responseText);
            }
        }

        //3.open 设置请求的方式和请求路径
        xmlhttp.open("get","${pageContext.request.contextPath}/ajax1?username=张三");

        //4.send 发送请求
        xmlhttp.send();
    }
</script>
```

对应的servlet

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    System.out.println("请求来了~~");
    response.getWriter().print("success~~");
}
```

- 常用方法:

```
open("请求方式","请求路径"[,"是否异步"]):设置请求的方式和请求的路径
send(["参数"]):发送请求 参数是请求方式为post的时候的参数。get请求的参数在open方法的请求路径上
setRequestHeader("content-type","form表单enctype属性"):设置post请求的参数的类型 必须放在send方法之前.
```

### post方法:需要设置请求头

```jsp
<script type="text/javascript">
    function btnClick(){
        //核心对象
        xmlhttp=null;
        if (window.XMLHttpRequest)
        {// code for IE7, Firefox, Opera, etc.
            xmlhttp=new XMLHttpRequest();
        }
        else if (window.ActiveXObject)
        {// code for IE6, IE5
            xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
        }

        //编写回调函数
        xmlhttp.onreadystatechange=function(){
            if(xmlhttp.readyState==4 && xmlhttp.status==200){
                alert(xmlhttp.responseText);
            }
        }

        //open。如果post请求的参数设置在这里，像get一样的话，会产生乱码。
        xmlhttp.open("post","/day15/ajax2");

        //如果是post请求，而且参数没有设置在open的链接里的话。就需要设置请求头,即设置文件的mime类型。这个类型是form表单的enctype参数的值
        xmlhttp.setRequestHeader("content-type", "application/x-www-form-urlencoded");

        //send
        xmlhttp.send("username=张三");
    }
</script>
```

- servlet中get,post对应的方法

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    // 接受参数
    String username = request.getParameter("username");
    username = new String(username.getBytes("iso8859-1"), "utf-8");
    System.out.println(username);

    // 响应数据
    response.setContentType("text/html;charset=utf-8");
    response.getWriter().println("姓名:" + username);
}

/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse
	 *      response)
	 */
protected void doPost(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    System.out.println(request.getMethod());
    request.setCharacterEncoding("utf-8");

    // 接受参数
    String username = request.getParameter("username");
    System.out.println(username);

    // 响应数据
    response.setContentType("text/html;charset=utf-8");
    response.getWriter().println("post姓名:" + username);

}
```

## 步骤分析:

```
1.数据库和表
2.新建项目
    导入 jar包 工具类 配置文件
3.新建一个注册页面 表单 在用户名文本框上输入用户名 失去焦点（获取事件）
    发送ajax请求,将输入的值发送到servlet
4.checkUsername4Ajax
    接受用户名
    调用service完成查询操作 返回一个用户
    判断user是否为空
        若为空 :写1 代表可以使用
        若不为空:写0
5.在表单接受响应的数据
    判断一下,
        若为0,则提示用户名已被占用 表单不可用提交 提交按钮禁用
            document.getElementById("sub").disabled=true;
```

# 案例2-使用jquery的ajax判断用户是否被占用

- 技术分析:

​	jquery中的ajax（jquery是js的类库，对ajax也进行了封装）

- 方法有四种:

1. 了解:jquery对象.load(url,params,function(数据){});   数据随便写个名字，就是回调的数据。

   ```jsp
   //var params="username=张三";  load方法如果用这个方式就是get请求，中文会乱码。
   //load方法如果用下面json这个方式就是post请求，中文不会乱码。
   var params={"username":"张苏纳"}; 
   //load方式
   /* $(this).load(url,params,function(d){
       alert(d);
   }); */
   ```

2. ★: $.get(url,params,function(数据){},type);  （\$应该换成对象，不然就是全局的）

   **发送get请求的ajax**

```
    url:请求的路径
    params:请求的参数 参数为key\value的形式 （key=value 或 json形式{"":"","":""}）
    fn:回调函数 参数就是服务器发送回来的数据
    type:返回内容格式，xml, html, script, json, text, _default。    以后用"json"
```

3. ★: $.post(url,params,function(数据){},type);

   **发送post请求的ajax**

   ```
   若结果为json格式,  打印返回值的时候是一个对象 
       例如若json为 {"result":"success","msg":"成功"}
       获取msg 只需要	参数.msg
   ```

4. 理解: $.ajax([选项]);

```
		选项的可选值:
			url:请求路径
			type:请求方法
			data:发送到服务器的数据
			success:fn(值是一个函数) 成功以后的回调
			error:fn 异常之后的回调
			dataType:返回内容格式 经常使用json
			async:设置是否是异步请求
		例如:
			$.ajax({
				url:"/day15/demo1",
				type:"post",
				data:"username=tom",
				success:function(d){
					alert(d.msg);
				},
				error:function(){},
				dataType:"json"
				
			});
```

- 步骤分析:

​	将js原生ajax修改成jquery的ajax

1. js代码

```jsp
<script type="text/javascript">
    $(function(){
        $("#btn").click(function(){
            var url="/day15/jqueryAjax";
            //var params="username=张三";  load方法如果用这个方式就是get请求，中文会乱码。
            //load方法如果用下面json这个方式就是post请求，中文不会乱码。
            var params={"username":"张苏纳"}; 
            //load方式
            /* $(this).load(url,params,function(d){
				alert(d);
			}); */

            //get方式
            /* $.get(url,params,function(d){
				alert(d)
			}); */

            //post方式.这里设置了type是json，所以servlet返回json格式可直接获取到。不然直接alert(d)就是原本的字符串
            /* $.post(url,params,function(d){
				alert(d.msg);
			},"json"); */

            //ajax 方式
            $.ajax({
                url:url,
                type:"post",
                data:params,
                success:function(d){
                    alert(d.msg);
                },
                error:function(){},
                dataType:"json"
            });
        });		
    })
</script>
</head>
<body>
    <input type="button" id="btn" value="点我">
</body>
```

2. servlet代码，针对上面的post

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    request.setCharacterEncoding("utf-8");
    System.out.println("请求方式:"+request.getMethod());

    //接受参数
    String username = request.getParameter("username");
    System.out.println(username);
    //response.getWriter().print("success");

    //{"result":"success","msg":"成功"}，\是转义字符
    String s="{\"result\":\"success\",\"msg\":\"成功\"}";

    //响应数据,设置编码
    response.setContentType("text/html;charset=utf-8");
    response.getWriter().print(s);
}
```

- 重写代码

```jsp
<script type="text/javascript">
    $(function(){
        //给username派发一个失去焦点事件 发送ajax请求
        $("input[name='username']").blur(function(){
            //获取输入的值
        var $value=$(this).val();
            //alert($value);
       $.get("/day15/checkUsername4Ajax","username="+$value,function(d){
                //alert(d);
        if(d==1){
           $("#usename_msg").html("<font color='green'>用户名可以使用</font>");
        }else{
           $("#usename_msg").html("<font color='red'>用户名已被使用</font>");
          }
         });
      });
   })
</script>
```

# 案例3-模仿百度搜索

- 需求:

​	在一个文本框中输入一段内容,keyup（键盘弹起事件）的时候发送一个ajax请求,去数据库中查找相应的内容,在页面上展示

- 步骤分析:

1. 表
2. 页面
3. 在文本框输入内容 keyup的时候 发送ajax请求 将输入值传递到后台
4. 将返回的数据展示

- 百度搜素页面的jsp

```jsp
<script type="text/javascript">
    $(function(){
        //文本框keyup的时候发送ajax
        $("#tid").keyup(function(){
            //获取文本框的值
            var $value=$(this).val();

            //内容为空的时候不发送ajax
            if($value!= null && $value!=''){
                //清空div
                $("#did").html("");

                $.post("/day15/searchKw","kw="+$value,function(d){
                    //不为空的时候切割字符串
                    if(d!=''){
                        var arr=d.split(",");
                        $(arr).each(function(){
                            //alert(this);
                            //可以将每一个值放入一个div 将其内部插入到id为did的div中。因为这里是追加，所以每次进去要刷新
                            $("#did").append($("<div>"+this+"</div>"));
                        });
                        //将div显示
                        $("#did").show();
                    }
                });
            }else{
                //内容为空的时候 将div隐藏 
                $("#did").hide();
            }
        });
    })
</script>
<title>Insert title here</title>
</head>
<body>
    <center>
        <div>
            <h1>黑马搜索</h1>
            <div>
                <input name="kw" id="tid"><input type="button" value="黑马一下">
            </div>
            <!--下面这一行是输入关键字后在搜索框下显示的框-->
            <div id="did" style="border: 1px solid red;width: 171px;position:relative;left:-34px;display:none"></div>
        </div> 
    </center>
</body>
```



# 案例4-省市联动

- 需求:

​	先有一个省份的下拉选,根据选择省份,从而动态的市下拉选中加载所有的市.

- 步骤分析:
  1. 表
  2. 页面上有两个下拉选 省份的下拉选一般是固定的。页面加载的时候读取所有的省份
  3. 当省份改变的时候,获取省份的信息,发送一个ajax请求,去市的表中查询相应省份的所有市,然后将他们加载到市下拉选上
  4. selectProServlet               selectCityServlet

```jsp
<script type="text/javascript">
	$(function(){
		//页面加载成功 查询所有的省
		$.get("/day15/selectPro",function(d){
			//alert(d)
			var $pro=$("#proId");
			$(d).each(function(){ 
				// 追加数据，格式按照下面的来
				$pro.append($("<option value="+this.provinceid+">"+this.name+"</option>"));
			});
		},"json");
		
		
		//给省份下拉选派发change事件
		$("#proId").change(function(){
			//获取省份id
			var $pid=$(this).val();
			//alert($pid);
			//发送ajax请求 查询所有的市
			$.get("/day15/selectCity",{"pid":$pid},function(obj){
				//alert(obj);
				var $city=$("#cityId");
				$city.html("<option>-请选择-</option>");
				if(obj!=null){
					$(obj).each(function(){
						$city.append($("<option value='"+this.cityid+"'>"+this.name+"</option>"));
					});
				}
			},"json");
		});
	})
</script>
<title>省市联动</title>
</head>
<body>
	<select id="proId" name="province">
		<option>-省份-</option>
		<!-- 这是追加后要形成的格式
		<option value="1">北京</option>
		 -->
	</select>
	<select id="cityId" name="city">
		<option>-请选择-</option>
	</select>
</body>
```

## json

​	JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。它基于ECMAScript的一个子集。

- json格式:

```
格式1:value可以为任意值
    {"key":value,"key1":value1}
格式2:e可以为任意值
    [e1,e2]
```

- **json的作用**

  1. 简化用JavaScript定义对象的方式

     ```
     <script type="text/javascript">
     	var json={"username":"tom","age":18};
     	alert(json.age);
     </script>
     ```

  2. 也能用在AJAX中，做为数据载体之一

- **json的语法作用**

1. 一个对象用{}表示
2. 一对属性之间，使用:来分隔，属性结束后，使用,来分隔，最后一个除外
3. 一个数组，使用[]符号

- jsonlib工具类,可以使对象转换成json数据

1. 导入jar包
2. 使用api

```
JSONArray.fromObject(对象)  对象是：数组和list  
JSONObject.fromObject(对象) 对象是：bean和map	
```






