# System类

系统类，可以查看系统属性。

System:类中的方法和属性都是静态的。

out：标准输出，默认是屏幕

in：标准输入，默认是键盘

## 方法

### 1）获取系统属性：Properties prop = System.getProperties()

Properties 是hashtable的子类，即map集合的一个子类对象，那么可以用map的方法取出该集合的元素。

每次启动的时候虚拟机都要去获取的信息，所以每次获取都不是一样的。

```
public static void demo_1(){
		//获取系统的属性信息，并存储到了Properties集合中。 
		/*
		 * properties集合中存储都是String类型的键和值。
		 * 最好使用它自己的存储和取出的方法来完成元素的操作。
		 */
		Properties prop = System.getProperties();
		// 该类的特有方法
		Set<String> nameSet = prop.stringPropertyNames();
		
		for(String name : nameSet){
			String value = prop.getProperty(name);
			System.out.println(name+"::"+value);
		}
}
```

### 2）如何在系统中自定义一些特有信息

```
//给系统设置一些属性信息。这些信息是全局，其他程序都可以使用。 
System.setProperty("myclasspath", "c:\myclass");
```

### 3）获取指定属性信息

```
String value = prop.getProperty("os.name");
print(value);
```

### 4）虚拟机启动时动态加载一些属性信息

```
执行代码时用这个命令：java -Dkey=value class文件名
```

# Runtime类

- 没有构造方法摘要，说明该类不可以创建对象。
- 又发现还有非静态的方法。说明该类应该提供静态的返回该类对象的方法。该方法是static Runtime getRuntime()
- 而且只有一个，说明Runtime类使用了单例设计模式。

## 方法exec

### - 执行exec

在单独的进程中执行命令，但是因为要传入参数可能会有异常，所以要抛出异常。

```
r.exec("k:\\notepad.exe");
```

不写路径也可以，因为会在系统路径下去找

```
r.exec("notepad.exe");
```

### - 杀掉进程destroy

```
public static void main(String[] args) throws IOException, InterruptedException {
	Runtime r = Runtime.getRuntime();
//		execute: 执行。 xxx.exe 
	
	// 用记事本打开test.java文件
	r.exec("notepad.exe  test.java")
	Process p = r.exec("notepad.exe");
	Thread.sleep(5000);
	p.destroy();
}
```

# Data类

## 1. Data()构造:打印格式不好看，希望有格式

```:
public static void methodDemo_1() {
	Date date = new Date();//将当前日期和时间封装成Date对象。
	System.out.println(date);//Sun Apr 29 11:48:02 CST 2012
}
```

## 2. DataFormat:自定义格式

对日期对象进行格式化。将日期对象-->日期格式的字符串。使用的是DateFormat类中的format方法。

```
public static void methodDemo_2() {
	Date date = new Date();
	//风格自定义
	SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy--MM--dd");
	String str_date = dateFormat.format(date);	
	System.out.println(str_date);
}
```

## 3. 日期格式的字符串-->日期对象

```
/**
* 将日期格式的字符串-->日期对象。
*     使用的是DateFormat类中的parse()方法。
* @throws ParseException
*/
public  static void methodDemo_3() throws ParseException {
    String str_date = "2012年4月19日";
    DateFormat dateFormat = DateFormat.getDateInstance(DateFormat.LONG); //Thu Apr 19 00:00:00 CST 2012
	str_date = "2011---8---17";
    dateFormat = new SimpleDateFormat("yyyy---MM---dd");   // Wed Aug 17 00:00:00 CST 2011

    Date date = dateFormat.parse(str_date);
    System.out.println(date);
}
```

## 4.日期对象-->日期格式的字符串。

    /**
     * 对日期对象进行格式化。
     * 将日期对象-->日期格式的字符串。
     *     使用的是DateFormat类中的format方法。
     */
    public static void methodDemo_2() {
        Date date = new Date();
        //获取日期格式对象。具体着默认的风格。 FULL LONG等可以指定风格。
        DateFormat dateFormat = DateFormat.getDateInstance(DateFormat.LONG);  //2018年8月7日
        dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG,DateFormat.LONG); //2018年8月7日 下午05时02分04秒
    
        //如果风格是自定义的如何解决呢？
        dateFormat = new SimpleDateFormat("yyyy--MM--dd"); //2018--08--07
        String str_date = dateFormat.format(date);
    
        System.out.println(str_date);
    }
## 5.日期对象和毫秒值之间的转换。

```
	/**
     * 日期对象和毫秒值之间的转换。
     *
     * 毫秒值-->日期对象 ：
     *     1，通过Date对象的构造方法  new Date(timeMillis);
     *  2，还可以通过setTime设置。
     *  因为可以通过Date对象的方法对该日期中的各个字段(年月日等)进行操作。
     *
     *
     * 日期对象-->毫秒值：
     *     2，getTime方法。
     * 因为可以通过具体的数值进行运算。
     *
     *
     */
    public static void methodDemo_1() {
        long time = System.currentTimeMillis();//
//    System.out.println(time);//1335671230671

        Date date = new Date();//将当前日期和时间封装成Date对象。
        System.out.println(date);//Sun Apr 29 11:48:02 CST 2012

        Date date2 = new Date(1335664696656l);//将指定毫秒值封装成Date对象。
        System.out.println(date2);
    }
```

# Calendar类

## 1. 获取时间

year是静态的，所以前面要写好类名。

```
public static void showDate(Calendar c) {
	Calendar c = Calendar.getInstance();

	int year = c.get(Calendar.YEAR);
	int month = c.get(Calendar.MONTH)+1; // 计算机的月是0-11
	int day = c.get(Calendar.DAY_OF_MONTH); 
	int week = c.get(Calendar.DAY_OF_WEEK); //计算机的星期是从星期日开始的，并认为是第一天。所以查表法要把星期日放在角标为1的地方
			
	System.out.println(year+"年"+month+"月"+day+"日"+getWeek(week));
}

//week用查表法
public static String getWeek(int i) {
		String[] weeks = {"","星期日","星期一","星期二","星期三","星期四","星期五","星期六"};
		return weeks[i];
	}
```

## 2. 设置时间

```
public static void showDays(int year) {
	Calendar c = Calendar.getInstance();
	//设置时间为2012.2.1
	c.set(2012, 2, 1);
	//把时间进行前后推移，以下是把天数-1.
	c.add(Calendar.DAY_OF_MONTH, -1);	
	showDate(c);
}
```

# Math类

ceil：大于指定数的最小整数

floor：小于指定数的最大整数

round：四舍五入

random：[0,1) (包含0，不包含1)之间的随机数。java.util里面也提供一个Random类