# StringBuffer

## 基础

StringBuffer:  就是**字符串缓冲区**，是一个容器，用于存储数据的容器。缓冲区可以不断修改

### 特点 vs  数组（也是容器）：

1，长度是可变的。      数组不可变
2，可以存储不同类型数据。（byte,short没有）     数组只能有一种类型
3，最终要转成字符串进行使用。
4，可以对字符串进行修改。		  

### 既然是一个容器对象。应该具备什么功能呢？--CURD增删改查

1，添加：
		  StringBuffer append(data);     // 末尾添加
		  StringBuffer insert(index,data);  //在当前字符串的Index位置添加data
2，删除：
		  StringBuffer delete(start,end):包含头，不包含尾。
		  StringBuffer deleteCharAt(int index):删除指定位置的元素 
3，查找：
		 char charAt(index);
  		  int indexOf(string);
		  int lastIndexOf(string);

​		  String   substring(int start,int end)

 4，修改：
		  StringBuffer replace(start,end,string);
		  void setCharAt(index,char);		  

5，反转：

​		  StringBuffer reverse()

6，将缓冲区中指定数据存到指定字符数组中

getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin) : 取原来字符串的srcB-srcE(含头不含尾)，存到dst中，从dstB开始存

**增删改查  C(create)U(update)R(read)D(delete)**

# StringBuilder

jdk1.5以后出现了功能和String

Buffer**一模一样（方法都一样）**的对象。就是StringBuilder

### 不同的是：

StringBuffer是线程同步的（相当于有锁）。通常用于多线程。
StringBuilder是线程不同步的。**通常用于单线程。** 它的出现提高效率。 

### jdk升级目的：

1，简化书写。
2，提高效率。
3，增加安全性。

# 基本数据类型对象包装类

基本数据类型对象包装类的最常见作用：基本数据类型和字符串类型之间做转换

### 基本数据类型转成字符串

基本数据类型 + “”

基本数据类型包装类.toString(基本数据类型值)

e.g. Integer.toString(34)   //将整数34变成“34”

### 字符串转成基本数据类型

// 静态调用方法

xxx    a =   Xxx.parseXxx(String)

int a = Integer.parseInt("123")

double b = Double.parseDouble("12.2")

boolean b = Boolean.parseBoolean("true")

// 非静态调用

int num = a.intValue();

### 十进制转成其他进制的字符串

toBinaryString()

toHexString()

toOctalString()

### 其他进制转成十进制

Integer.parseInt(string, radix)  //将string的数字按照radix进制转换成十进制数

### 自动装箱，拆箱

```
Integer x = new Integer(4);

Integer x = 4;  //对4自动装箱，相当于new Integer(4)
x = x +2 //对x自动拆箱，相当于 x.intValue() + 2. 
Integer x =null;
x = x +2 //此时会报错，因为x是空指针，没有intValue方法
```

### Integer的范围

```
Integer a = new Integer(128);
Integer b = new Integer(128);
		
System.out.println(a==b);//false
System.out.println(a.equals(b));//true,比较的是值
		
Integer x = 127;//jdk1.5以后，自动装箱，如果装箱的是一个字节以内的数，那么该数据会被共享不会重新开辟空间。
Integer y = 127;
System.out.println(x==y);  //true
System.out.println(x.equals(y));//true
```

