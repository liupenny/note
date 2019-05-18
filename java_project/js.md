JavaScript一种**直译式脚本语言**，是一种动态类型、弱类型、**基于原型**的语言，内置支持类型。它的**解释器**被称为JavaScript引擎，**为浏览器的一部分(就是不用安装别的解释器)**，广泛用于客户端的脚本语言。

## 组成部分:

​	ECMAScript:js基础语法(规定 关键字 运算符 语句 函数等等...)
	BOM:浏览器对象模型
	DOM:文档对象模型

## 作用:

​	修改html页面的内容
	修改html的样式
	完成表单的验证

- 注意:

		js可以在页面上直接写,也可以单独出去
	js的文件的后缀名 .js

- js和html整合

		方式1:在页面上直接写
		将js代码放在 <script></script>标签中,一般放在head标签中
	方式2:独立的js文件
		通过script标签的src属性导入

## 语法

- js中变量声明:

   var 变量名=初始化值;
   var 变量名;
   	变量名=初始化值;
   注意:
   	var可以省略 建议不要省略
   	一行要以分号结尾,最后一个分号可以省略,建议不要省略

- js的数据类型:

   原始类型:(5种)
   	Null
   	String
   	Number
   	Boolean
   	Undefined
   	通过 typeof运算符可以判断一个值或者变量是否属于原始类型,若属于原始类型,他还可以判断出属于那种原始类型
   		typeof 变量|值;
   	若变量为null,使用typeof弹出的值 object

   	使用typeof的返回值
   	undefined - 如果变量是 Undefined 类型的 
   	boolean - 如果变量是 Boolean 类型的 
   	number - 如果变量是 Number 类型的 
   	string - 如果变量是 String 类型的 
   	object - 如果变量是一种引用类型或 Null 类型的 
   	
   	引用类型（就是Js对象，在js高级语法的文档中找）:
   			Number Boolean
   			★String
   				属性:length
   				方法:
   					substring
   			★Array
   				属性:length
   				方法:
   					join(分隔符):打印数组
   			Math
   			Date
   			RegExp
   				直接量语法: 
   					/正则表达式/
   				方法:
   					test()
   			全局
   				decodeURI
   				encodeURI
   				
   				eval()

### js:事件驱动函数（不会自然发生，得先发生某件事情，然后这个事情才会发生）

- 函数定义格式:

     方式1:
     	function 函数名(参数){
     			函数体;
     		}
     	注意:函数不用声明返回值类型 
     		参数不需要加类型
     		函数调用的时候
     			函数名(参数)

     方式2，匿名函数:

     ​		function(){....}

-  js中的事件:

   常见的事件:

   ​	焦点:
   		onfocus
   		onblur

   ​	单击:  

   ​		onclick
   	表单提交: 

   ​		onsubmit 加在form表单上的 onsubmit="return 函数名()"  注意函数返回值为boolean类型，就是返回true才能提交，否则不提交。

   ​		onchange	

   ​	页面加载: 

   ​		onload

-  js获取元素:

   方式1:
   	var obj=documnet.getElementById("id值");

- 获取元素的value值

   obj.value;

- 获取元素的标签体中的内容

   obj.innerHTML;


- js事件和函数的绑定:

   方式1:
   	通过标签的事件属性   <xxx onclick="函数名(参数)"></xxx>
   方式2:
   	给元素派发事件		

   ​		dom对象.onxxx=function(){...}

   ​		document.getElementById("id值").onclick=function(参数){....}
   		document.getElementById("id值").onclick=函数名
   	注意:
   		内存中应该存在该元素才可以派发事件
   	a.将方式2的js代码放在html页面的最下面
   	b.在页面加载成功之后在运行方式2的js代码  onload事件.

### 案例3-轮播图片

需求:
	每隔3秒图片更新一下
技术分析:
	bom中window对象的定时器方法
定时器:
	var id=setInterVal(code,毫秒数):每隔指定的毫秒数执行一次函数 周期
	var id=setTimeout(code,毫秒数):延迟指定的毫秒数之后 只执行一次函数
	

	清除定时器:
		clearInterval(id);
		claerTimeout(id);
String对象
	原始类型的String是一个为对象可以直接调用String类对象的方法
	substring(0,endIndex);
////////////////
步骤分析:
	1.在首页上面绑定一个onload事件
	2.事件绑定的函数中编写一个定时器
	3.定时器每隔3秒更换图片
		imgObj.src="";
///////////////
补充:
	运算符:
		比较运算符: > >= < <=
		若两边都是数字 和java一样
		若一边为数字,另一边为字符串形式的数字,将字符串形式的数字转换成数字在进行比较   3>"2"
		若一边为数字,另一边为字符串,返回一个false   3>"hello"
		两边都是字符串的时候,比较ascii
	等性运算符 == ===
		== :只判断值是否相同
		===:不仅判断是否相同,还要判断类型是否相同
语句:
	if语句 和java一样
	for while 语句和java一样
	switch 和java一样(区别,switch 后面跟字符串. 还可以跟变量)


## 总结:掌握

​	1.css和html整合
		方式3种 
	2.css中选择器:
		id class 元素
		属性 后代 
	3.js
		js和html整合
			方式两种
	4.变量定义
	5.函数定义
		2中格式
	6.事件
		onclick onload onsubmit
	7.事件和函数的绑定
		2中方式
	8.定时器 2种
	9.for while if