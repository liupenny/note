# html:

​	作用:展示

## 文件标签:

​		<html>
			<head>
				<title></title
			</head>
			<body></body>
		</html>

## 排版标签:

​		p 段落
		hr 水平线
		br 换行

## 其他标签

### 字体

​		h1~h6 标题
		font 字体 大小 颜色
		b strong 加粗
		i 斜体

### 图片★

​		<img src="图片的路径" alt="替代文字" width="" height=""/>
		路径的写法:
			相对路径:
				./ 或者 什么都不写  当前目录
				../ 上一级目录
			绝对路径:常用
				http://www.itheima.com/xx
			alt:它规定在图像无法显示时的替代文本。

### 超链接★

​		<a href="跳转的路径" target="在什么地方打开"></a>

### 表格标签 ★★

​	<table border="" width="" height="" bgcolor="" align="表格对齐方式">
		<tr align="内容对齐方式">
			<th></th>  	<!--表头单元格-->
			<td></td> 	<!--普通单元格-->
		</tr>
	</table>
	
​	td重要的属性:
		colspan:列合并
		rowspan:行合并

列表:
	<ol></ol>
	<ul></ul>
	
子标签:列表项
		<li></li>

### form 表单标签:

​		常用属性
			action:提交的路径
			method:提交的方式(get和post)
		常用子标签
			input
			select
			textarea

- input标签

​		常用属性:
			type:
				text
				password
				radio
				checkbox
				file
				
​				submit
				reset
				button
				
​				hidden
				image
			name:
				-要想将信息提交到服务器必须提供name属性
				-将单选框和复选框设置成一组
			
​			value:
				text password  设置默认值
				radio checkbox 设置选中后提交的值
				submit reset button 给按钮起个显示的名字

- select:下拉选

​		<select name="">
			<option value="">显示的名字</option>
		</select>
	

- textarea:文本域

​		<textarea cols="" rows="" name="">默认值</textarea>
	
​	给单选框多选框设置默认值
		设置属性 checked="checked"
	给下拉选设置默认值
		设置属性 selected="selected"
	

- 默认值:

​			text password:设置value属性
			radio checkbox:设置 checked="checked"属性
			select:在option上设置 selected="selected"属性
			value可以设置按钮的展示文字
		
​	提交的路径(get)
		http://ssdfsdfsfd?key=vaule&key=value
	例如:
	http://localhost/day/login.jsp?username=tom&password=123
				

- 框架(了解)

​	frameset:规定框架结构  框架集
		常用属性:
			rows:水平qiege
			cols:垂直切割
		常用子标签	
			frame:具体实现
			
​		例如:水平切割3份
			<frameset rows="15%,*,10%">
				<frame>
				<frame>
				<frame>
			</frameset>
	frame:
		常用的属性:
			src:具体展示那个网页
			name:给当前的frame起个名称 方便超链接使用