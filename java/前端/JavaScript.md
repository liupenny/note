# JavaScript

基于对象和事件取得的脚本语言，主要应用在客户端。

1：交互性（信息的动态交互）

2：安全性（不允许直接访问本地硬盘）

3：跨平台性（只要是可以解释JS的浏览器就可以执行，和平台无关）

目前文档不多，参考JScript的就行

# JavaScript和Java不同

1：JS是Netscape公司的产品，前身是LiveScript；Java是Sun公司的产品，现在是Oracle的

2：JS是基于对象（所有东西在内存里都是对象），Java是面向对象（用对象的思考方式）

3：JS只需解释就可以执行，Java需要先编译成字节码文件再执行

**4：JS是弱类型，Java是强类型。** 

强类型：对每一种数据都定义好类型，并对该类型所分配的空间进行好定义

弱类型：

# JavaScript和Html的结合方式

1，将javascript代码封装到<script>标签中。
2，将javascript代码封装到js文件中，并通过<script>中的src属性进行导入。

注意：如果<script>标签中使用src属性，那么该标签中封装的javascript代码不会被执行。 

所以通常导入js文件都是用单独<script>来完成。

```
	<!--导入一个js文件-->
	<!--script type="text/javascript" src="demo.js"></script-->
```

# JS语法

通常高级程序设计语言所包含的语法内容：
1，关键字:该种语言中被赋予了特殊含义的单词。
2，标示符:用于标示数据和表达式的符号。通常可以理解为在程序中自定义的名称。比如变量名，函数名。
3，注释:注解说明解释程序。用于调试程序。
4，变量:用于标示内存中一片空间。用于存储数据，该空间的中的数据是可以变化的。 
什么时候使用变量：当数据不确定的时候。
5，运算符：可以让数据进行运算的符号。
6，语句:用于对程序的运行流程进行控制的表达式。
7，函数:用于对功能代码进行封装，便于提高复用性。
8，数组:对多数据进行存储，便于操作。就是传说中容器。
9，对象:只要是基于对象的语言，或者是面向对象的语言，就存在着对象的概念，
对象就是一个封装体。既可以封装数据有可以封装函数。 

- 这些都是高级程序设计语言具备的共性内容，只不过各种语言对这些内容的表现形式有所不同。但是使用基本思想是一致。 

## 4.变量

- js中定义变量。使用到关键字：var  
- 定义变量。Js是弱类型的。
- 注意"" , ' '  都是字符串，可以互相嵌套

```javascript
var x = 4;//var不写也行，因为js是非严谨的语言，但是我们开发的时候建议都按照严谨的方式来定义。
x = "abc";//重新赋值为字符串abc。
x = 3.45;//赋值为小数。其实也是数字类型。
x = true;//赋值为boolean类型。
x = 'c';//赋值为字符串c。
alert("x="+x);//这是一个函数，将具体的参数通过对话框进行显示。
```

## 5.运算符

```
/*
* 运算符。
* 1，算术运算符。
* 		+ - * / % ++ --
* 2，赋值运算符。
* 		=  += -= *=  /= %= 
* 3，比较运算符:运算的结果要么是false要么是true。
* 		> < >= <= != ==
* 4，逻辑运算符。用来连接两个boolean型的表达式。
* 		!  &&  ||
* 5，位运算符。
* 		&  |  ^  >> << >>>
* 6，三元运算符。
* 		? :
*/
```

### 注意：JS是弱类型，所以a/1000是3.71

```
var a = 3710;
alert("a="+a/1000*1000);//a=3710;
```

- 在js中false就是0，或者null，非0，非null。就是true。默认用1表示。

## 细节

```
/*
* 一些细节：
* 1,undefined:未定义。其实它就是一个常量。
*/
var xx;
alert(xx);//undefined
alert(xx==undefined);//true

//要想获取具体的值的类型。可以通过typeof来完成。
alert(typeof("abc")=='string');//string
alert(typeof(2.5)=='number');//number//判断类型。
alert(typeof(true));//boolean
alert(typeof(78));//number
alert(typeof('9'));//string
```

## 8.数组

```
/*
* javascript中的数组演示。
* 
* 数组用于存储更多的数据，是一个容器。 
* 特点：
* 1，长度的是可变的。
* 2，元素的类型是任意的。
* 建议在使用数组时，存储同一类型的元素。操作起来较多方便。 

* js中的数组定义的两种方式：
* 1，var arr = []; var arr = [3,1,5,8];
* 2，使用了javascript中的Array对象来完成的定义。 
* 		var arr = new Array();//var arr = [];
* 		var arr1 = new Array(5);//数组定义并长度是5.
* 		var arr2 = new Array(5,6,7);//定义一个数组，元素是5,6,7;
*/
```

## 7.函数

```
js中的函数演示。
* 函数：就是一个功能的封装体。
* 定义功能通常需要两个明确。
* 1，功能的结果。
* 2，功能实现中的参与运算的未知的内容。
* 
* 
* js中的定义函数的格式。
* 通过指定的关键字来定义。
* function 函数名(参数列表){
* 		函数体：
* 		return 返回值;//如果没有具体的返回值，return语句可以省略不写。
```

### 细节1：

```
/*
* 函数的一些细节。
* 1，只要使用函数的名称就是对这个函数的调用。show函数调用若只传递4，就会打印y是undefined 
* 2，函数中有一个数组在对传入的参数进行存储。这个数组就是arguments
*/
function show(x,y){
//	alert(arguments.length);
//	alert(x+":"+y);
for(var a=0;a<arguments.length; a++){
	document.write(arguments[a]);
}
}

//show(4,5,6,9,7,4);//建议函数中定义几个参数就传递几个实参。
```

### 细节2：

```
<script type="text/javascript">
/*
* 函数的细节2：
*/

function getSum(){
return 100;
}

//	var sum = getSum();//调用函数，getSum函数运行，并将返回的结果赋值给sum。
var sum = getSum;//getSum本身是一个函数名，而函数本身在js中就是一个对象。getSum就是这个函数对象的引用.
//将getSum这个引用的地址赋值给sum。这时sum也指向了这个函数对象。
//相当于这个函数对象有两个函数名称。

//	alert("sum="+sum);//打印时候如果sum指向的是函数对象，那么会将该函数对象的字符串表现形式打印出来，就是该函数的代码定义格式。
// js中函数对象的字符串形式就是该函数的代码会展示出来

function show2(){
	alert("show2 run");
}
//	alert(show2());
</script>
```

### 动态函数

```
<script type="text/javascript">
/*
* js函数的其他表现形式。
* 动态函数:使用的是js中内置的一个对象Function.
* 只不过用的不是很多。 
* 参数列表，函数体都是通过字符串动态指定的。 
*/

// 这是一个函数对象，x,y为函数参数
var add = new Function("x,y","var sum; sum=x+y; return sum;");
var he = add(4,8);

//	alert("he="+he);

function add2(x,y){
var sum ;
sum = x+y;
return sum;
}
</script>
```

### 匿名函数

```
<script type="text/javascript">
/*
* 匿名函数：没有名字的函数。 
* 通常是函数的简写形式。
*/

var add3 = function (a,b){
return a+b;
}

alert(add3(7,8));

function haha()
{
alert("haha run");
}

var xixi = haha;

//上述代码可以简写成下面的方式。 
var xixi = function()
{
alert("haha run");
}
</script>
```

## 9.对象

### Global对象：

把所有全局方法集中在一个对象中。

parseInt(str) ：将数字型的字符串转换成整数，从最开始开始看，有数字就返回数字

```
var val = parseInt("aaa")   //返回非法值  NaN
// 很神奇的是！！！！
var val = parseInt("12aaa")   //返回12
```

### Number对象

因为js是基于对象的，所以你写的什么都是对象。下面这个num4也是对象，可以直接调用toString方法。

```
/*
* 将指定进制格式的字符串转成十进制。 
*/
var num = parseInt("110",2);
println("num="+num);

var num1 = parseInt('0x3c',16);
println("num1="+num1);


//将十进制转成其他进制。使用数字对象完成。一个数字就是Number对象，不需要显示创建
var num3 = new Number(6);
println("num3="+num3.toString(2));  //110

var num4 = 60;
println("num4="+num4.toString(16));  //3c
```

### Object：

toString  和 valueOf 函数的返回值一样的

### String

```
/*
* 发现js中的string对象方法有限，想要对字符串操作的其他功能。 
* 比如：去除字符串两端的空格。这时只能自定义。
*/
//去除字符串两端的空格。
function trim(str){

//定义两个变量，一个记录开始的位置。一个记录结束的位置。
//对开始的位置 的字符进行判断，如果是空格，就进行递增，直到不是空格为止。
//对结束的位置 的字符进行判断，如果是空格，就进行递减，直到不是空格为止。
//必须要保证开始<=结束，这样才可以进行截取。

var start,end;
start=0;
end=str.length-1;

while(start<=end && str.charAt(start)==' '){
start++;
}
while(start<=end && str.charAt(end)==" "){
end--;
}

return str.substring(start,end+1);
}
```

- 既然trim方法是用来操作字符串的方法，可不可以像字符串已有的方法一样，将该方法也定义到字符串对象中呢？直接用字符串对象调用即可。 

- 这里就可以使用一个该字符串的原型属性来完成：

  1. 原型：就是该对象的一个描述。该描述中如果添加了新功能。那么该对象都会具备这些新功能。
  2. 而prototype就可以获取到这个原型对象。通过prototype就可以对对象的功能进行扩展。 

- 需求：想要给string对象添加一个可以去除字符串两端空格的新功能. 就可以使用原型属性来完成。

- 给string的原型中添加一个功能。 注意：给对象添加新功能直接使用  “对象.新内容”   即可。

  ```
  String.prototype.len = 199;//给string的原型对象中添加一个属性 名为len。值为199.
  String.prototype.trim = trim; //即上面的函数，可以同名; 也可以写匿名对象 String.prototype.trim = function() ; 也可以把所有prototype封装在一个js文件内后
  
  //添加行为。
  //	println("abc".len);
  //	alert("-"+"     ab cd     ".trim()+"-");
  ```

### 自定义对象：

思路：

1. 如果想要自定义对象，应该先对对象进行描述。
2. js是基于对象，不是面向对象的。不具备描述事物的能力。
3. 我们还想按照面向对象的思想编写js。
4. 就要先描述，在js中，可以用函数来模拟面对对象的中的描述。

过程：

1. js中用function描述对象，相当于构造器
2. 动态给p对象添加属性。直接使用p.属性名即可。
3. 如果定义的p对象的属性赋值为一个函数，即是给p对象添加一个方法。使用 p.func = function(){ 函数体} 即可

#### 初步想法

```javascript
<script type="text/javascript">
//用js来描述人
function Person(){//相当于构造器。
//	alert("person run");
}

//通过描述进行对象的建立。 new.
var p  = new Person();

//动态给p对象添加属性。直接使用p.属性名即可。
p.name = "zhangsan";
p.age = 29;
//如果定义的p对象的属性赋值为一个函数，即是给p对象添加一个方法。
p.show = function(){
alert("show :"+this.name+":"+this.age);
}

//	p.show();
</script>
```

#### 将对象封装在函数中：

- print打印的时候，打印的x是p中的属性名字：name,age,setName,getName。（函数也是属性）
- 不能通过调用p.x打印p对象x属性的值，p.x表示p对象有一个名字叫做x的属性，等价于p["x"] ，写成p[x]，就获取的是x表示的字符串

```javascript
function Person(name,age){
    //在给Person对象添加了两个属性。this不能省
    this.name = name;
    this.age = age;
    
	// 要写this,标明是本对象
    this.setName = function(name){
        this.name = name;
    }
    this.getName = function(){
        return this.name;
    }
}

var p = new Person("旺财",20);
//
for(x in p){
    println(x+":"+p[x]);
}

p.setName("小强");
//	alert(p.name);
//	alert(p.getName());
```

#### 直接用{}定义对象实体，相当于一个集合，可通过[]取元素：

```javascript
//直接使用{}定义属性和值的键值对方式。键值键通过:连接，键与键之间用逗号隔开。 
var pp  = {
    //定义一些成员。
    "name":"小明","age":38,
    "getName":function(){
        return this.name;
    }	
}

//对象调用成员有两种方式：对象.属性名    对象["属性名"]
//	alert(pp["age"]+":"+pp.name);
```

#### 用js实现键值对映射关系的集合容器。

- 如果是实体用Oxx表示
- bX(布尔型), iN（整形）, s（字符串）

```
//用js实现键值对映射关系的集合容器。 
var oMap = {
    8:"小强",3:"旺财",7:"小明"
}

//	var val1 = oMap[8];
//	alert("val1:"+val1);

var val2 = get(7);
//	alert("val2:"+val2);

function get(key){
    return oMap[key];
}

```

#### map的封装对象

```
//	name="lisi";
//	age = 30;

// 方法一
var myobj = {
    myname:"lisisi",myage:30
}
alert(myobj.myname+":"+myobj["myage"]);

// 方法二
var myobj2 = {
    "myname2":"hahahah","myage2":48
}
alert(myobj2.myname2+":"+myobj2["myage2"]);

// 值是数组，值是map
var myMap = {
//	names:["lsii1","lisi2","lisi4"],nums:[34,10,48]
// names键对应的值是数组，数组中又是map
    names:[{name1:"zhangsan"},{myname:"hahahah"}]
}
alert(myMap.names[1]);
alert(myMap.names[0].name1);
```

#### 创建一个Object：

```
var obj = new Object();
obj.name = "god father";
obj.age = 2012;

//	alert(obj.name+":"+obj.age);
```

## 6.语句

### with：

为了简化对象调用内容的书写，可以使用js中的特有语句with来完成。

格式：

```
with(对象)
{
	在该区域中可以直接使用指定的对象的内容，不需要写对象
}
```

```javascript
var year = date.getFullYear();
//			var month = date.getMonth()+1;
//			var day = date.getDate();
//			var week = getWeek(date.getDay());
with(date){
    var year = getFullYear();
    var month = getMonth()+1;
    var day = getDate();
    var week = getWeek(getDay());

    println(year+"-----"+month+"---+--"+day+"-----"+week);
}

```

### for in语句：

格式：

```
for(变量  in 对象)//对对象进行变量的语句。 
{
}
```

举例：

```
var arr = [32,80,65];
//对数组遍历，i是数组的角标
for(i in arr){
println("i="+arr[i]);
}
println("<hr/>");

for(x in numObj){
	println(x);
}
			
```
