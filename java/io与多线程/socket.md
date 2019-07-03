# Socket

- socket就是为网络服务提供的一种机制
- 通信两端都有socket

## UDP：DatagramSocket

### 一：测试

#### 1.发送端

创建UDP传输的发送端。
思路：
1，建立udp的socket服务。
2，将要发送的数据封装到数据包中。 
3，通过udp的socket服务将数据包发送出去。
4，关闭socket服务。

注意：要抛出异常

```java
public static void main(String[] args) throws IOException {
    System.out.println("发送端启动......");
    //1,udpsocket服务。使用DatagramSocket对象。
    DatagramSocket ds = new DatagramSocket(8888);
    //2,将要发送的数据封装到数据包中。
    String str = "udp传输演示：哥们来了！";
    //使用DatagramPacket将数据封装到的该对象包中。
    byte[] buf = str.getBytes();

    DatagramPacket dp = 
        new DatagramPacket(buf,buf.length,InetAddress.getByName("192.168.1.100"),10000);
=
    //3，通过udp的socket服务将数据包发送出去。使用send方法。
    ds.send(dp);

    //4，关闭资源。
    ds.close();
}
```

#### 2.接收端

建立UDP接收端的思路。
1，建立udp socket服务,因为是要接收数据，必须要明确一个端口号。
2，创建数据包，用于存储接收到的数据。方便用数据包对象的方法解析这些数据.
3，使用socket服务的receive方法将接收的数据存储到数据包中。
4，通过数据包的方法解析数据包中的数据。
5，关闭资源 

```java
public static void main(String[] args) throws IOException {
    System.out.println("接收端启动......");
    
    //1,建立udp socket服务。
    DatagramSocket ds = new DatagramSocket(10000);
    //2,创建数据包。
    byte[] buf = new byte[1024];
    DatagramPacket dp = new DatagramPacket(buf,buf.length);
    //3,使用接收方法将数据存储到数据包中。
    ds.receive(dp);//阻塞式的。
    //4，通过数据包对象的方法，解析其中的数据,比如，地址，端口，数据内容。
    String ip = dp.getAddress().getHostAddress();
    int port = dp.getPort();
    String text = new String(dp.getData(),0,dp.getLength());

    System.out.println(ip+":"+port+":"+text);

    //5,关闭资源。
    ds.close();
}
```

### 二：一直收发消息

发送：如果把发送地址改为192.168.1.255（广播地址），自己就也会收到信息

```java
public static void main(String[] args) throws IOException {
    DatagramSocket ds = new DatagramSocket();
    BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
    String line = null;

    while((line=bufr.readLine())!=null){
        byte[] buf = line.getBytes();
        DatagramPacket dp = 
            new DatagramPacket(buf,buf.length,InetAddress.getByName("192.168.1.100"),10000);
        ds.send(dp);
        if("886".equals(line))
            break;
    }

    //4，关闭资源。
    ds.close();
}
```

接收端

```java
public static void main(String[] args) throws IOException {
    DatagramSocket ds = new DatagramSocket(10000);
    
    while(true){
        //2,创建数据包。
        byte[] buf = new byte[1024];
        DatagramPacket dp = new DatagramPacket(buf,buf.length);

        //3,使用接收方法将数据存储到数据包中。
        ds.receive(dp);//阻塞式的。
        
        String ip = dp.getAddress().getHostAddress();
        int port = dp.getPort();
        String text = new String(dp.getData(),0,dp.getLength());

        System.out.println(ip+":"+port+":"+text);
    }
    //服务器就不关了
}
```

### 三：聊天程序

接收数据、发送数据要同时执行，就需要多线程

发送端：

```java
public class Send implements Runnable {
    private DatagramSocket ds;
    public Send(DatagramSocket ds){
        this.ds = ds;
    }

    @Override
    public void run() {
        try {
            BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
            String line = null;

            while((line=bufr.readLine())!=null){
                byte[] buf = line.getBytes();
                DatagramPacket dp = 
                    new DatagramPacket(buf,buf.length,InetAddress.getByName("192.168.1.255"),10001);
                ds.send(dp);
                if("886".equals(line))
                    break;
            }
            ds.close();
        } catch (Exception e) {
            rhrow new RuntimeException("发送失败")
        }
    }
}
```

接收端：

```java
public class Rece implements Runnable {
    private DatagramSocket ds;
    public Rece(DatagramSocket ds) {
        this.ds = ds;
    }

    @Override
    public void run() {
        try {
            while (true) {
                // 2,创建数据包。
                byte[] buf = new byte[1024];
                DatagramPacket dp = new DatagramPacket(buf, buf.length);
                // 3,使用接收方法将数据存储到数据包中。
                ds.receive(dp);// 阻塞式的。
                // 4，通过数据包对象的方法，解析其中的数据,比如，地址，端口，数据内容。
                String ip = dp.getAddress().getHostAddress();
                int port = dp.getPort();
                String text = new String(dp.getData(), 0, dp.getLength());

                System.out.println(ip + "::" + text);
                if(text.equals("886")){
                    System.out.println(ip+"....退出聊天室");
                }
            }
        } catch (Exception e) {

        }
    }
}
```

主函数

```
public class ChatDemo {
public static void main(String[] args) throws IOException {
		DatagramSocket send = new DatagramSocket();
		
		DatagramSocket rece = new DatagramSocket(10001);
		new Thread(new Send(send)).start();
		new Thread(new Rece(rece)).start();
	}
}
```

## TCP：Socket

客户端：该对象在建立时，就可以去连接特定主机，因为tcp是面向连接的，所以必须先启动服务端，连接服务端。形成通路后，就可以通过该套接字获取其输入，输出流。在该通道进行数据的传输。

服务器：

```java
public static void main(String[] args) throws IOException {
    //		服务端接收客户端发送过来的数据，并打印在控制台上。 
    /*
		 * 建立tcp服务端的思路：
		 * 1，创建服务端socket服务。通过ServerSocket对象。
		 * 2，服务端必须对外提供一个端口，否则客户端无法连接。
		 * 3，获取连接过来的客户端对象。
		 * 4，通过客户端对象获取socket流读取客户端发来的数据 
		 * 		并打印在控制台上。
		 * 5，关闭资源。关客户端，关服务端。 （可选）
		 */

    //1创建服务端对象。
    ServerSocket ss = new ServerSocket(10002);

    //2,获取连接过来的客户端对象。
    Socket s = ss.accept();

    String ip = s.getInetAddress().getHostAddress();

    //3，通过socket对象获取输入流，要读取客户端发来的数据
    InputStream in = s.getInputStream();
    
    byte[] buf = new byte[1024];
    int len = in.read(buf);
    String text = new String(buf,0,len);
    System.out.println(ip+":"+text);

    //使用客户端socket对象的输出流给客户端返回数据
    OutputStream out = s.getOutputStream();
    out.write("收到".getBytes());

    s.close();
    ss.close();
}
```

客户端：

```java
public static void main(String[] args) throws UnknownHostException, IOException {
    //客户端发数据到服务端
    /*
		 * Tcp传输，客户端建立的过程。
		 * 1，创建tcp客户端socket服务。使用的是Socket对象。
		 * 		建议该对象一创建就明确目的地。要连接的主机。 
		 * 2，如果连接建立成功，说明数据传输通道已建立。
		 * 		该通道就是socket流 ,是底层建立好的。 既然是流，说明这里既有输入，又有输出。
		 * 		想要输入或者输出流对象，可以找Socket来获取。 
		 * 		可以通过getOutputStream(),和getInputStream()来获取两个字节流。
		 * 3，使用输出流，将数据写出。 
		 * 4，关闭资源。 
		 */
    Socket socket = new Socket("192.168.1.100",10002);
    OutputStream out = socket.getOutputStream();	
    out.write("tcp演示：哥们又来了!".getBytes());

    //读取服务端返回的数据,使用socket读取流。 
    InputStream in = socket.getInputStream();
    byte[] buf = new byte[1024];
    int len = in.read(buf);
    String  text = new String(buf,0,len);
    System.out.println(text);
    //关闭资源。
    socket.close();
}
```

## 练习：创建一个英文大写转换服务器.

- 客户端输入字母数据，发送给服务端
- 服务端收到后显示在控制台，并将该数据转成大写返回给客户端 
- 直到客户端输入over.转换结束. 
- 有客户端和服务端,使用tcp传输

客户端思路：

1，需要先有socket端点。
2，客户端的数据源：键盘。
3，客户端的目的：socket.
4，接收服务端的数据，源：socket。
5，将数据显示在打印出来：目的：控制台.
6，在这些流中操作的数据，都是文本数据。
转换客户端:
1，创建socket客户端对象。
2，获取键盘录入。
3，将录入的信息发送给socket输出流。

```java
public static void main(String[] args) throws UnknownHostException, IOException {
    //1,创建socket客户端对象。
    Socket s = new Socket("192.168.1.100",10004);
    //2，获取键盘录入。
    BufferedReader bufr  =
        new BufferedReader(new InputStreamReader(System.in));
    //3,socket输出流。
  	BufferedWriter bufOut = new BufferedWriter(new OutputStreamWriter(s.getOutputStream()));

    //4,socket输入流，读取服务端返回的大写数据
    BufferedReader bufIn  = new BufferedReader(new InputStreamReader(s.getInputStream()));

    String line = null;
    while((line=bufr.readLine())!=null){
        if("over".equals(line))
            break;
		bufOut.write(line);
        // readLine在读到回车符的时候才会判断这一行结束,所以要写上换行
        bufOut.newLine(line);

        //读取服务端发回的一行大写数。
        String upperStr = bufIn.readLine();
        System.out.println(upperStr);
    }
	bufr.close();
    s.close(); //在socket流里面加了-1
}
```

转换服务端分析：

1，serversocket服务。

2，获取socket对象。

3，源：socket，读取客户端发过来的需要转换的数据。

4，目的：显示在控制台上。

5，将数据转成大写发给客户端。 

```java
public static void main(String[] args) throws IOException {
    ServerSocket ss = new ServerSocket(10004);

    //2,获取socket对象。
    Socket s = ss.accept();

    //获取ip.
    String ip = s.getInetAddress().getHostAddress();
    System.out.println(ip+"......connected");

    //3,获取socket读取流，并装饰。 
    BufferedReader bufIn = new BufferedReader(new InputStreamReader(s.getInputStream()));
	BufferedWriter bufOut = new BufferedWriter(new OutputStreamWriter(s.getOutputStream()));
    //4,获取socket的输出流，并装饰。
  

    String line = null;
    while((line=bufIn.readLine())!=null){ //接收到-1后就自动停了
        bufOut.write(line.toUpperCase());
        bufOut.newLine();
    }

    s.close();
    ss.close();
}
```

### 问题：

客户端和服务端都在莫名的等待。

因为客户端和服务端都是阻塞式方法，没有读到结束标记，就会一直等。所以写数据的时候一定要写换行

```
bufOut.write(line.toUpperCase());
bufOut.newLine();
```

### 改用printWriter可以自动刷新

服务器

```
public static void main(String[] args) throws IOException {
		ServerSocket ss = new ServerSocket(10004);
		//2,获取socket对象。
		Socket s = ss.accept();
		//获取ip.
		String ip = s.getInetAddress().getHostAddress();
		System.out.println(ip+"......connected");
		
		//3,获取socket读取流，并装饰。 
		BufferedReader bufIn = new BufferedReader(new InputStreamReader(s.getInputStream()));
		
		//4,获取socket的输出流，并装饰。
		PrintWriter out = new PrintWriter(s.getOutputStream(),true);
		
		String line = null;
		while((line=bufIn.readLine())!=null){
			System.out.println(line);
			out.println(line.toUpperCase());
		}
		s.close();
		ss.close();	
	}
}
```

客户端：

```
public static void main(String[] args) throws UnknownHostException, IOException {
		//1,创建socket客户端对象。
		Socket s = new Socket("192.168.1.100",10004);
		
		//2，获取键盘录入。
		BufferedReader bufr  =
				new BufferedReader(new InputStreamReader(System.in));

		PrintWriter out = new PrintWriter(s.getOutputStream(),true);
		
		//4,socket输入流，读取服务端返回的大写数据
		BufferedReader bufIn  = new BufferedReader(new InputStreamReader(s.getInputStream()));
		String line = null;
		while((line=bufr.readLine())!=null){			
			if("over".equals(line))
				break;

			out.println(line);
			
			//读取服务端发回的一行大写数。
			String upperStr = bufIn.readLine();
			System.out.println(upperStr);
		}
		s.close();		
	}
```

## 练习：文件拷贝

客户端：

```java
public static void main(String[] args) throws UnknownHostException, IOException {
    System.out.println("上传客户端。。。。。。");
    
    File file = new File("c:\\client.txt");
    System.out.println(file.exists());
    Socket s = new Socket("192.168.1.100",10005);
    BufferedReader bufr =
        new BufferedReader(new FileReader(file));
    PrintWriter out = new PrintWriter(s.getOutputStream(),true);

    String line = null;
    while((line=bufr.readLine())!=null){

        out.println(line);
    }

    //告诉服务端，客户端写完了。
    s.shutdownOutput();
    //		out.println("!@#$%^&*(");

    BufferedReader bufIn = new BufferedReader(new InputStreamReader(s.getInputStream()));

    String str = bufIn.readLine();
    System.out.println(str);

    bufr.close();
    s.close();
}
```

服务端：

```java
public static void main(String[] args) throws IOException {
    System.out.println("上传服务端。。。。。。。。。");
    ServerSocket ss = new ServerSocket(10005);
    Socket s = ss.accept();
    System.out.println(s.getInetAddress().getHostAddress()+".....connected");
    BufferedReader bufIn = new BufferedReader(new InputStreamReader(s.getInputStream()));
    BufferedWriter bufw = new BufferedWriter(new FileWriter("c:\\server.txt"));

    String line = null;
    while((line=bufIn.readLine())!=null){
        //			if("over".equals(line))
        //				break;
        bufw.write(line);
        bufw.newLine();
        bufw.flush();
    }

    PrintWriter out = new PrintWriter(s.getOutputStream(),true);
    out.println("上传成功");

    bufw.close();
    s.close();
    ss.close();
}
```

### 问题：没有文件标志会两边都等待！

- 以over为发送结束标识符：万一文件中有over?
- 发送前先协商一个字符串作为结束标志：麻烦
- 发送前先协商当前时间戳作为结束标志：保证唯一性
- 使用socket自带的方法`s.shutdownOutput();` 这样服务端就会自动关闭流。

## 练习：并发图片上传服务器

客户端：

```java
public static void main(String[] args) throws UnknownHostException, IOException {
    // 预先处理
    File file = new File(args[0]);
    if(!(file.exist() && file.isFile())
       print("文件不存在或有问题")
    if(!(file.getName().endWith(".jpg"))
       print("图片格式错误")
    if(file.length()>1024*1024*5) //5M
       print("文件过大")
       
    //1,创建客户端socket。
    Socket s = new Socket("192.168.1.100",10006);

    //2,读取客户端要上传的图片文件。
    FileInputStream fis = new FileInputStream("c:\\0.bmp");

    //3,获取socket输出流，将读到图片数据发送给服务端。
    OutputStream out = s.getOutputStream();

    byte[] buf = new byte[1024];
    int len = 0;
    while((len=fis.read(buf))!=-1){
        out.write(buf,0,len);
    }

    //告诉服务端说：这边的数据发送完毕。让服务端停止读取。
    s.shutdownOutput();
    //读取服务端发回的内容。 		
    InputStream in  = s.getInputStream();
    byte[] bufIn = new byte[1024];

    int lenIn = in.read(buf);
    String text = new String(buf,0,lenIn);
    System.out.println(text);

    fis.close();
    s.close();
}
```

服务端：明确每个客户端要被执行的代码，将该代码存入run方法中即可。

```java
public static void main(String[] args) throws IOException {
    //创建tcp的socket服务端。
    ServerSocket ss = new ServerSocket(10006);
    while(true){
        Socket s = ss.accept();			
        new Thread(new UploadTask(s)).start();		
    }
    //获取客户端。
    //		ss.close();
}
```



```java
public class UploadTask implements Runnable {
    private static final int SIZE = 1024*1024*2;
    private Socket s;

    public  UploadTask(Socket s) {
        this.s = s;
    }

    @Override
    public void run() {
        int count = 0;
        String ip = s.getInetAddress().getHostAddress();
        System.out.println(ip + ".....connected");
        try{
            // 读取客户端发来的数据。
            InputStream in = s.getInputStream();

            // 将读取到数据存储到一个文件中。
            File dir = new File("c:\\pic");
            if (!dir.exists()) {
                dir.mkdirs();
            }
            File file = new File(dir, ip + ".jpg");
            //如果文件已经存在于服务端 
            while(file.exists()){
                file = new File(dir,ip+"("+(++count)+").jpg");
            }

            FileOutputStream fos = new FileOutputStream(file);
            byte[] buf = new byte[1024];
            int len = 0;
            while ((len = in.read(buf)) != -1) {
                fos.write(buf, 0, len);
                if(file.length()>SIZE){
                    System.out.println(ip+"文件体积过大");
                    fos.close();
                    s.close();

                    System.out.println(ip+"...."+file.delete());
                    return ;
                }
            }

            // 获取socket输出流，将上传成功字样发给客户端。
            OutputStream out = s.getOutputStream();
            out.write("上传成功".getBytes());

            fos.close();
            s.close();
        }catch(IOException e){
        }
    }
}
```

注意

1. 防止文件被覆盖----命名要不一样，用count标识。

   文件名中的ip根据客户端确定的，只有count不一样，先判断一下如果当前文件名存在，就给count++.否则以当前文件名命名。

   不能把count设为成员变量：会有多个线程访问的读写问题，即：192.1.253.44（1）.jpg &  192.1.253.45（2）.jpg

2. 判断文件过大----进行限制

## 网络操作知识

- uri：范围比URL大，树后面的二维码也算

- URL对象：将域名所代表的http包中的信息封装成一个对象

- URLConnection对象：获取url对象的Url连接器对象。代表应用程序和 URL 之间的通信链接 。将连接封装成了对象:java中内置的可以解析的具体协议的对象+socket.

  URLConnection conn = url.openConnection();

  就不用自己写socket了。

- serversocket的backlog：能连接到服务器的最大连接数

- DNS解析：先在本地hosts找，例如你修改127.0.0.1对应域名是www.baidu.cn  就直接访问你本地的网页。

- hosts中一个ip地址可以对应很多域名

- 屏蔽恶意网站：可以在hosts中加对应关系：127.0.0.1   网站域名。这样该网站就在本地找网页了。