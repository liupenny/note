# validate和bootstap

## validate:jquery的插件

### 案例1-表单校验

- 需求:

​	通过validate插件来校验表单

- 技术分析:

​	jqery validate

- validate使用步骤:

​	validate是别人封装好的第三方工具
	1.导入jquery.js
	2.导入validate.js
	3.在页面加载成功之后 对表单进行校验  $("选择器").validate()
	4.在validate中编写校验规则
		$("选择器").validate({
			rules:{},  规则
			messages:{}  提示信息，可选项
		});

- rules格式:

​		格式1:
			字段的name属性:"校验器"
		格式2:				
			字段的name属性:{校验器:值,校验器:值}
	

```
例如:
		username:"required",
		password:{
			required:true,
			digits:true
		},
		repassword:{
			equalTo:"[name='password']"
		},
		zuixiaozhi:{
			min:5
		},
		shuzhiqujian:{
			range:[5,10]
		}
```

#### 1.1.1      检验器查询表

| 校验类型    | 取值           | 描述               |
| ----------- | -------------- | ------------------ |
| required    | true\|false    | 必填字段           |
| email       | “@”或者”email” | 邮件地址           |
| url         |                | 路径               |
| date        | 数字           | 日期               |
| dateISO     | 字符串         | 日期（YYYY-MM-dd） |
| number      |                | 数字（负数，小数） |
| digits      |                | 整数               |
| minlength   | 数字           | 最小长度           |
| maxlength   | 数字           | 最大长度           |
| rangelength | [minL,maxL]    | 长度范围           |
| min         |                | 最小值             |
| max         |                | 最大值             |
| range       | [min,max]      | 值范围             |
| equalTo     | jQuery表达式   | 两个值相同         |
| remote      | url路径        | ajax校验           |

- messages的格式:

​		格式1:
			字段的name属性:"提示信息"
		格式2:
			字段的name属性:{校验器:"提示信息",校验器:提示信息"}

```
例如:
		username:"用户名不能为空",
		password:{
			required:"密码不能为空",
			digits:"密码只能是数字"
		},
		repassword:{
			equalTo:"两次输入的内容不一致"
		},
		zuixiaozhi:{
			min:"最小值应该大于{0}" //动态获取上面填的最小值，大括号里面写上面第几个值的索引
		},
		shuzhiqujian:{
			range:"输入的范围在{0}~{1}之间"
		}
		sex: required 出现的提示在前面
		1. <input type="radio" name="sex" value="男" checked="checked"> 给个默认值，想取消也取消不掉
		2. <input type="radio" name="sex" value="女" />女
		<label for="sex" class="error"></label>  添加一个label标签，作为错误提示的展示。class=error是人家写好的展示类型
```

### 案例2-创建一个响应式的页面

需求:
	创建一套页面,可以根据上网设备的不同自动调节显示的效果.
技术分析:
	bootstrap
bootstrap（2对手机支持不太好，用3）:
	webcss框架,
	集合了html/css/jquery为一家
	创建响应式的页面
	响应式:适配不同的上网设备
bootstarp的入门
	1.下载bootstarp
		网站:http://www.bootcss.com/
		下载:用于生产环境（可以直接上线）的 Bootstrap
	2.导入bootstarp.css
	3.导入jquery.js
	4.导入bootstrap.js
	5.添加一个meta标签 支持移动设备，移动设备优先
	<meta name="viewport" content="width=device-width, initial-scale（初始化是否缩放）=1">
	6.要想适应所有设备：将所有的内容放入到布局容器中.
	Bootstrap 需要为页面内容和栅格系统包裹一个 .container 容器。我们提供了两个作此用处的类
	方式1:
		<div class="container"></div>
	方式1:
		<div class="container-fluid"></div>

#### 注意:

- bootstrap将每一行分成12份（每个标签自己内部的行，比如一行两个div,第二个div还可以按照12进行划分）

- 媒体查询（当访问页面时，自己判断出是什么上网设备）:

  - 假如大屏幕,每行显示6个

  ​	超大电脑,屏幕分辨率>1200		使用: col-lg-2(样式columb-large-每个2份，-后面的数字可以自己改)		

  - 假如屏幕小点,每行显示4个

  ​		992<屏幕分辨率<1200			使用: col-md-3

  - 再小点,每行显示2个

  ​		768<屏幕分辨率<992			使用: col-sm-6

  - 继续小,每行显示1个（手机）	

​		屏幕分辨率<768				使用:col-xs-12

- bootstrap组成部分:

​	全局css样式--定义了一套css样式
	组件--定义了很多可以直接使用的组件 例如:字体图标 导航条 
	js插件--例如:轮播图 选项卡

- 步骤分析:

​	1.先有一个模版页面 0.html
	2.先创建一个导航条
	3.下面创建一个轮播图
	4.下面再创建3个div
		中等屏幕的时候 3个在一行
		小屏幕的时候 2个一行
		最小屏幕的时候1个一行

```
栅格系统：
<div style="border": 1px solid red;" class="col-lg-1 col-md-2 col-sm-6 col-xs-6>1</div>
多个样式中间用空格分开即可，这样对屏幕进行动态缩放时，会根据屏幕的大小进行页面的展示。（最大的页面每行显示12个图标，md展示6个，sm2个，最小的2个）
如果只有class="col-lg-1",就是一旦当前页面分辨率<1200后，所有其他大小的页面都每行只显示1个图标。
```

#### bootstrap组成部分:

​	全局css样式--定义了一套css样式
	组件--定义了很多可以直接使用的组件 例如:字体图标 导航条 。自带响应式效果（即会根据屏幕的大小自动变化）
	js插件--例如:轮播图 选项卡

### 案例3-布局首页

需求:
	当屏幕为小屏幕的时候和超小屏幕的时候隐藏热门商品中的左边那张图片
	当屏幕为超小屏幕隐藏middle图片
技术分析:
	hidden-xxxx
步骤分析:
	1.布局页面
	2.创建8个div
	3.第一个:logo
		3个div 
		小屏幕 2个一行 
		超小屏幕一个一行
	4.第二个div:导航条
	5.第三个div:轮播图
	6.第四个div:最新商品
		先分成左右两个div
			左边的div 放一张图片 
				屏幕为小屏幕的时候和超小屏幕的时候 隐藏
				中等屏幕 占2份
			右边的div 
				中等屏幕 占10份
				屏幕为小屏幕的时候和超小屏幕的时候 占12份
					middle 
						中等屏幕 6份
						小屏幕	12份
						超小屏幕 隐藏
					商品:
						中等屏幕 2份
						小屏幕 	 4份
						超小屏幕 12份