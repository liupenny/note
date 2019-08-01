# IO流

## 介绍

- 流按操作数据分为两种：字节流和字符流

- 字节流的抽象基类：InputStream, OutputStream

  字符流的抽象基类：Reader, Writer

  由这四个类派生出来的子类名称都是以其父类名作为子类名的**后缀**，前缀名就是对象的功能

数据最常见的体现形式：文件

- 字符流的由来：
  其实就是：字节流读取文字字节数据后，不直接操作而是先查指定的编码表。获取对应的文字。
  在对这个文字进行操作。简单说：字节流+编码表 

# 字符流

**系统有默认编码表，**所以直接读取字节进行转换。

## 举例1：将一些文字存储到硬盘一个文件中。

- 文件可能不存在，所以要抛出异常
- 如果没有写fw.close，且忘记flush,那已经写的数据就会丢失
- 写了close 会在关闭前自动刷新
- close和flush的区别：flush刷新后，流还能继续用，close刷新后，流会关闭
- 如果构造函数中加入true，可以实现对文件进行续写！否则就会覆盖原来对象
- 想要加换行符，windows中要写fw.write("xixi\r\nhhh");

```
public static void main(String[] args) throws IOException {
//创建一个可以往文件中写入字符数据的字符输出流对象。
* 既然是往一个文件中写入文字数据，那么在创建对象时，就必须明确该文件(用于存储数据的目的地)。
* 如果文件不存在，则会自动创建。
* 如果文件存在，则会被覆盖。
* 如果构造函数中加入true，可以实现对文件进行续写！
*/
FileWriter fw = new FileWriter("demo.txt",true);

/*
* 调用Writer对象中的write(string)方法，写入数据。 
* 其实数据写入到临时存储缓冲区中。
*/
fw.write("abcde"+LINE_SEPARATOR+"hahaha");
//		fw.write("xixi");

/*
* 进行刷新，将数据直接写到目的地中。
*/		
//		fw.flush();

/*
* 关闭流，关闭资源。在关闭前会先调用flush刷新缓冲中的数据到目的地。
*/
fw.close();

//fw.write("haha");// java.io.IOException: Stream closed
}
```

## 举例2：异常处理

要跟数据发生关系的，无论：读、写、创建都会抛出异常。

文件操作一旦出现异常，一定要关闭文件，放在finally中

```
public static void main(String[] args) {
	// 一定要把变量写在外面，这样全局代码都能用到
	FileWriter fw = null;
	try {
		fw = new FileWriter("k:\\demo.txt");
		fw.write("abcde" + LINE_SEPARATOR + "hahaha");
	} catch (IOException e) {
		System.out.println(e.toString());
	} finally {
		// 关闭也可能出现异常，例如上面根本找不到k盘，fw就是空
		if (fw != null)
			try {
				fw.close();
			} catch (IOException e) {
				// code....
				throw new RuntimeException("¹Ø±ÕÊ§°Ü");
			}
	}
}
```

- fw定义在外面---fw定义在try里面，catch和finally就没法操作了。
- finally中对关闭对象进行!=null判断---否则，fw没有找到文件就是null

## 举例3：filereader:用默认编码读取

- 已到达流的末尾，会返回-1,这是因为java识别到了硬盘上的分割符

### 总结：read方法到末尾返回-1，readLine返回Null

### 1.读字符

```
public static void main(String[] args) throws IOException {
//1,创建读取字符数据的流对象。
* 在创建读取流对象时，必须要明确被读取的文件。一定要确定该文件是存在的。 
* 用一个读取流关联一个已存在文件。 
*/
	FileReader fr = new FileReader("demo.txt");	
	int ch = 0;
	//已到达流的末尾，会返回-1
	while((ch=fr.read())!=-1){
		System.out.println((char)ch);
	}
		
	/*用Reader中的read方法读取字符。要是读取多个，就写多次，会自动往下读
	int ch = fr.read();
	System.out.println((char)ch);
	int ch1 = fr.read();
	System.out.println(ch1);
	*/
	fr.close();
}
```

### 2.读到字符数组中

- read方法返回读取到的字符个数，当文件指针已经到末尾，就会返回-1.
- 如果数组已经全部装满了，再次用该数组存会从头开始存，覆盖前面的数据，不会清空所有数据

```
public static void main(String[] args) throws IOException {
	FileReader fr = new FileReader("demo.txt");
	/*
	* 使用read(char[])读取文本文件数据。
	* 先创建字符数组。
	*/
	char[] buf = new char[1024];
	int len = 0;
		
	while((len=fr.read(buf))!=-1){
		//读了几个，打印几个
		System.out.println(new String(buf,0,len));
	}
		
	int num = fr.read(buf);//将读取到的字符存储到数组中。假设文件abcde,num长度3,
	System.out.println(num+":"+new String(buf,0,num)); //显示：3：abc
	int num = fr.read(buf);
	System.out.println(num+":"+new String(buf,0,num)); //显示：2：dec
		
	fr.close();
}
```

### 3. 将c盘的一个文本文件复制到d盘。

思路：

1. 在d盘创建一个文件，用于存储c盘文件的数据
2. 定义读取流和c盘文件相关联
3. 通过不断的读写完成数据存储
4. 关闭资源

```
public static void main(String[] args) {
	FileReader fr = null;
	FileWriter fw = null;
	try {
		fr = new FileReader("IO流_2.txt");
		fw = new FileWriter("copytest_2.txt");
		
		//创建一个临时容器，用于缓存读取到的字符。
		char[] buf = new char[BUFFER_SIZE];//这就是缓冲区。 
			
		//定义一个变量记录读取到的字符数，(其实就是往数组里装的字符个数 )
		int len = 0;	
		while((len=fr.read(buf))!=-1){
			fw.write(buf, 0, len);
		}	
	} catch (Exception e) {
		throw new RuntimeException("读写失败");
	}finally{
		if(fw!=null)
			try {
				fw.close();
			} catch (IOException e) {	
				e.printStackTrace();
			}
		if(fr!=null)
			try {
				fr.close();
			} catch (IOException e) {	
				e.printStackTrace();
			}
		}
	}
}
```

![](https://note.youdao.com/yws/public/resource/f2168540caf26c25109a3234bac42594/xmlnote/D7D6021B48044F4A8FB3D3F6ED16E457/51434)

## 字符流的缓冲区

### BuferWriter

- 缓冲区的出现是为了提高流的操作效率而出现的，所以在创建缓冲区之前，必须先要有流对象。
- 而真正进行写操作的是流对象。所以关闭缓冲区就是关闭流对象
- newLine()：提供一个跨平台的行分割符
- 写一行，刷一下。防止突然断电等问题

```
public static void main(String[] args) throws IOException {
	FileWriter fw = new FileWriter("buf.txt");	
	//创建了一个字符写入流的缓冲区对象，并和指定要被缓冲的流对象相关联
	//只要将需要被提高效率的流对象作为参数传递给缓冲区的构造函数即可
	BufferedWriter bufw = new BufferedWriter(fw);
	
	//为了提高写入的效率。使用了字符流的缓冲区。使用缓冲区的写入方法将数据先写入到缓冲区中。
//		bufw.write("xixiixii");
//		bufw.newLine();
		
		for(int x=1; x<=4; x++){
			bufw.write("abcdef"+x);
			bufw.newLine();
			// 写一行，刷一下。防止突然断电等问题
			bufw.flush();
		}
		
		//只要用到缓冲区，就要刷新。使用缓冲区的刷新方法将数据刷目的地中。
//		bufw.flush();
		
		//关闭缓冲区。其实关闭的就是被缓冲的流对象。
		bufw.close();
		
//		fw.write("hehe");
//		fw.close();
	}
```

### BufferReader

- 可以读一行：readLine()，不包含行终止符
- 返回null表示读到文件末尾

```
public static void main(String[] args) throws IOException {
	//创建了一个读取流对象，和文件相关联
	FileReader fr = new FileReader("buf.txt");
    //为了提高读取的效率。使用了字符流的缓冲区。将读取流对象作为参数传递给缓冲对象的构造函数
	MyBufferedReader bufr = new MyBufferedReader(fr);
	// 读到底，返回null
	String line = null;
		
	while((line=bufr.myReadLine())!=null){
			System.out.println(line);
		}
		
		bufr.myClose();
}
```

#### readLine的原理：底层还是用read一次读一个，到尾返回null

读一行获取多个字符，其实都是**在硬盘上一个一个读取**，所以最终使用的还是read一次读一个

其实内存中有个数组，每次读一个先存在内存中的数组中。读到行尾\r\n再返回

### 通过缓冲区复制一个文件

```
public static void main(String[] args) throws IOException {
	BufferedReader bufr = null;
	BufferedWriter bufw = null;
	
	try{
		bufr = new BufferReader(new FileReader("buf.txt"));		
		bufw = new BufferWriter(new FileWriter("buf_copy.txt"));
			
		String line = null;
		while((line=bufr.readLine())!=null){
			bufw.write(line);
			bufw.newLine();
			bufw.flush();
		}
	}
	catch(IOException e){
        throw new RuntimeException("读写失败")
	}
	finally{
		try{
			if(bufr!=null)
				bufr.close();
		}
         catch (IOException e) {	
				e.printStackTrace();
		}
		try{
			if(bufw!=null)
				bufr.close();
		}
         catch (IOException e) {	
				e.printStackTrace();
		}
	}
}
```

### 自定义一个读取缓冲区。即模拟一个BufferedReader.

```
public class MyBufferedReader{
	private FileReader r;
	MyBufferedReader(Reader r){
		this.r = r;
	}
	
	//一次读一行的方法
	public String myReadLine() throws IOException{
		// 定义一个临时容器，java的用字符数组，这里用Builder也行
		StringBuilder sb = new StringBuilder();
		
		int ch = 0;
		// 开始读字符
		while((ch = myRead())!=-1){
			//遇到了\r就不存，但还需要再往下读一次。
			if(ch=='\r')
				continue;
			if(ch=='\n')
				return sb.toString();
			//将从缓冲区中读到的字符，存储到缓存行数据的缓冲区中。
			sb.append((char)ch);
		}		
		//读到文件结尾，若最后一行没有换行符，还要判断下
		if(sb.length()!=0)
			return sb.toString();
		return null;
	}

	public void myClose() throws IOException {
		r.close();
	}
}
```

#### 装饰设计模式

- 当想要对已有的对象进行功能增强时，可以定义增强类，将已有对象传入，基于已有的功能，并提供加强功能。那么自定义的该类称为装饰类。
- 装饰类通常会通过构造方法接收被装饰的对象，并基于被装饰的对象的功能，提供更强的功能

举例：FileReader对象传递给自定义类MyBufferReader，MyBufferReader提供readLine方法，该方法调用了read，并增强了read 的功能。

```
class Person{
	public void eat(){
        print("吃饭")
	}
}

class SuperPerson{
	private Person p;
	SuperPerson(Person p){
        this.p = p;
	}
	public void eat(){
	    print("喝酒")
         p.eat()
         print("甜点")
	}
}
```

#### 装饰和继承的区别

- 首先有一个继承体系。

  Writer
  	|--TextWriter:用于操作文本
  	|--MediaWriter：用于操作媒体。

- 想要对操作的动作进行效率的提高。按照面向对象，可以通过继承对具体的进行功能的扩展。 效率提高需要加入缓冲技术。即对之前的类进行继承，在继承的类中实现缓冲技术

  Writer
  	|--TextWriter:用于操作文本
  		|--BufferTextWriter:加入了缓冲技术的操作文本的对象。
  	|--MediaWriter：用于操作媒体。
  		|--BufferMediaWriter:

- 如果这个体系进行功能扩展，有多了流对象。那么这个流要提高效率，是不是也要产生子类呢？是。这时就会发现只为提高功能，进行的继承，导致继承体系越来越臃肿。不够灵活。 

- 重新思考这个问题

  既然加入的都是同一种技术--缓冲。前一种是让缓冲和具体的对象相结合。 可不可以将缓冲进行单独的封装，哪个对象需要缓冲就将哪个对象和缓冲关联。

  ```java
  class Buffer{
      Buffer(TextWriter w){
      }
      Buffer(MediaWirter w){
      }
  }
  ```

- 上面这样写扩展性很差，找到参数的共同类型，通过多态的形式，提高扩展性。

  ```java
  class BufferWriter extends Writer{
      private Weiter w;
      BufferWriter(Writer w){
      }
  }
  ```

- 现在的体系
  Writer
  	|--TextWriter:用于操作文本
  	|--MediaWriter：用于操作媒体。
  	|--BufferWriter:用于提高效率，功能增强。

- 装饰和继承都能实现一样的特点：进行功能的扩展增强。 

- 特点：装饰类是增强已有对象，具备功能相同，只是增强。所以通常装饰类和被装饰类都必须所属同一个接口或者父类。 

- 装饰比继承灵活，避免了继承体系臃肿，降低了类与类之间的关系。

- 装饰设计模式：对一组对象的功能进行增强时，就可以使用该模式进行问题的解决。 

### LineNumerReader也是一个装饰类

也可以读取一行，可以设置行号

```java
public static void main(String[] args) throws IOException {
    FileReader fr = new FileReader("IO送_2.txt");
    LineNumberReader lnr = new LineNumberReader(fr);

    String line = null;
    lnr.setLineNumber(100);
    while((line=lnr.readLine())!=null){
        System.out.println(lnr.getLineNumber()+":"+line);
    }

    lnr.close();
}
```

#### 实现LineNumberReader:通过继承MyBufferedReader实现

```java
public class MyLineNumber extends MyBufferedReader{
    private int lineNumber;
    MyBufferedReader(Reader r){
        super(r);
    }

    //一次读一行的方法
    public String myReadLine() throws IOException{
        lineNumber++;
        return super.MyReadLine();
    }

    public int setLineNumber(int num){
        this.lineNumber = num;
    }

    public int getLineNumber(){
        return lineNumber;
    }
}
```

# 字节流

- 操作的是字节，字节数组
- "abcdefg".getBytes()  返回字符串的字节数组
- 不刷新也会保存。字符流底层用了字节流的缓冲区，比如一个中文有两个字节，需要输入完再去判断。所以字节流没有缓冲区，不需要刷新。
- 但是要close关闭
- 打印出来的数需要(char)类型转换

```java
public static void demo_read() throws IOException {

    //1，创建一个读取流对象。和指定文件关联。
    FileInputStream fis = new FileInputStream("bytedemo.txt");
	
    // 先获取文件的字节数，定义一个刚好的缓冲区，但数组大会溢出
    //		System.out.println(fis.available());
    //		byte[] buf = new byte[fis.available()];		
    //		fis.read(buf);
    //		System.out.println(new String(buf));
    
    //建议使用这种读取数据的方式
    //		byte[] buf = new byte[1024];		
    //		int len = 0;
    //		
    //		while((len=fis.read(buf))!=-1){
    //			System.out.println(new String(buf,0,len));
    //			f.write(buf,0,len)
    //		}


    //		int ch = 0;
    //		while((ch=fis.read())!=-1){
    //			System.out.println((char)ch);
    //		}

    //一次读取一个字节。
    //		int ch = fis.read();		
    //		System.out.println(ch);

    fis.close();

}

public static void demo_write() throws IOException {

    //1，创建字节输出流对象。用于操作文件.
    FileOutputStream fos = new 			     FileOutputStream("bytedemo.txt");

    //2,写数据。直接写入到了目的地中。 
    fos.write("abcdefg".getBytes());

    fos.close();//关闭资源动作要完成。 
}
```

## 1.available方法

- 得到文件的字节数，定义一个长度刚好的缓冲区

```java
System.out.println(fis.available());
byte[] buf = new byte[fis.available()];		
fis.read(buf);
System.out.println(new String(buf));
```

- 但是有可能会有内存溢出，所以这个方法要慎用

## 2.操作媒体文件，写字符串

因为需要查表，如果能查到就可以，差不到就会报错。

## 3.通过缓冲区操作mp3

```java
// 通过字节流的缓冲区完成复制，563ms
public static void copy_2() throws IOException {
    FileInputStream fis = new FileInputStream("c:\\0.mp3");	
    BufferedInputStream bufis = new BufferedInputStream(fis);

    FileOutputStream fos = new FileOutputStream("c:\\2.mp3");
    BufferedOutputStream bufos = new BufferedOutputStream(fos);

    int ch = 0;

    while((ch=bufis.read())!=-1){
        bufos.write(ch);
    }

    bufos.close();
    bufis.close();
}
```

## 4.自己实现字节流缓冲区

```java
public class MyBufferedInputStream
{
    private InputStream in;
    private byte[] buf = new byte[1024];
    private int pos, count = 0;

    MyBufferedInputStream(InputStream in){
        this.in = in;
    }

    // 一次读一个字节，从缓冲区（字符数组）获取
    public int myRead(){
        if(count == 0){
            count = in.read(buf);
            if(count < 0)
                return -1;
            pos = 0;
            byte b = buf[pos];

            count--;
            pos++;
            return b&255;
        }
        else if(count > 0){
            byte b = buf[pos];

            count--;
            pos++;
            return b;
        }
        return -1;
    }
}
```

- 上面代码可能会导致没有进行数据复制，因为mp3的第一个字节很可能是1111-1111，即-1。导致取第一个字节就是末尾

- b是byte类型，函数返回值是int，这样就可以对Byte进行类型提升，用4个字节来存储byte。但是系统自动提升前面会补1，导致Int型数据还是-1、所以我们要人为地在前面补0。 即和int 型的255与一下。

  1111-1111  1111-1111   1111-1111   1111-1111

   &  0000-0000  0000-0000   0000-0000    1111-1111  

- write方法会只写最低八位，保证原数据的有效性

# 读取键盘录入

- 读取键盘录入：

System.out：对应标准输出设备，控制台

System.in：对应标准输入设备，键盘

- in的类型是InputStream，字节流：

```
InputStream in = System.in;
// 字节流读到的是int类型（详情见4）
int b = in.read();
```

- in是阻塞式的，只有当前输入完才行。
- 键盘录入的结束：ctrl-c / 自定义结束标记

```java
/*
* 获取用户键盘录入的数据，
* 当录入一行数据后，就将该行数据变为大写打印
* 如果用户输入的是over，结束键盘录入。
* 
* 思路：
* 1，因为键盘录入只读取一个字节，要判断是否是over，需要将读取到的字节拼成字符串。
* 2，那就需要一个容器。StringBuilder.
* 3，在用户回车之前将录入的数据变成字符串判断即可。 
*/
public static void readKey2() throws IOException {
    //1，创建容器。
    StringBuilder sb = new StringBuilder();
    //2，获取键盘读取流。		
    InputStream in = System.in;
    //3，定义变量记录读取到的字节，并循环获取。 		
    int ch = 0;

    while((ch=in.read())!=-1){
        //	在存储之前需要判断是否是换行标记 ,因为换行标记不存储。 
        if(ch=='\r')
            continue;
        if(ch=='\n'){
            String temp = sb.toString();
            if("over".equals(temp))
                break;
            System.out.println(temp.toUpperCase());
            sb.delete(0, sb.length());
        }
        else
            //将读取到的字节存储到StringBuilder中。
            sb.append((char)ch);

        //	System.out.println(ch);
    }
}
```

- 上述代码繁琐，能否用readLine。readLine是字符流的方法，read是字节流的方法，看能否将字节流转换为字符流。

## InputStreamReader：字节转字符

用于操作字节流的字符流对象，参数接受一个字节流

```java
public static void main(String[] args) throws IOException {
    //字节流。 
    InputStream in = System.in;
    //将字节流对象转成字符流对象，使用转换流：InputStreamReader
    InputStreamReader isr = new InputStreamReader(in);
    //为了提高效率，将字符串进行缓冲区技术高效操作，使用BufferedReader
    BufferedReader bufr = new BufferedReader(isr);

    String line = null;

    while((line=bufr.readLine())!=null){
        if("over".equals(line))
            break;
        System.out.println(line.toUpperCase());
    }	
}
```

## OutputStreamWriter：字符转字节

用于操作字符流的字符流对象，参数接受一个字符流

```java
public static void main(String[] args) throws IOException {
    //输入流 ---  字节-->字符 ---  字符流缓冲区
    InputStream in = System.in;
    InputStreamReader isr = new InputStreamReader(in);
    BufferedReader bufr = new BufferedReader(isr);
	//输出流 ---  字符-->字节 ---  字节流缓冲区
    OutputStream out = System.out;
    OutputStreamWriter osw = new OutputStreamWriter(out);
    BufferedWriter  bufw = new BufferedWriter(osw);

    String line = null;

    while((line=bufr.readLine())!=null){
        if("over".equals(line))
            break;
        // \r\n不能跨平台
        //	osw.write(line.toUpperCase()+"\r\n");
     	// 不flush就写不进去
        //	osw.flush();

        bufw.write(line.toUpperCase());
        bufw.newLine();
        bufw.flush();
    }
}
```

- 简化一下：键盘录入的常见写法

```
BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
		
BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(System.out));		
```

## 练习：键盘录入--文件

```java
public static void main(String[] args) throws IOException {
    BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
    BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("b.txt")));

    String line = null;
    while((line=bufr.readLine())!=null){
        if("over".equals(line))
            break;

        bufw.write(line.toUpperCase());
        bufw.newLine();
        bufw.flush();
    }
}
```

# 修改io的源，目的

```
Systrm.setIn(new FileInputStream("s.java"))
Systrm.setOut(new PrintStream("z.txt"))
```