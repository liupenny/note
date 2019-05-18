# JDBC

## jdbc介绍

概述：

-  JDBC（Java Data Base Connectivity,java数据库连接）是一种用于执行SQL语句的Java API。可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。是Java访问数据库的标准规范。JDBC提供了一种基准,据此可以构建更高级的工具和接口，使数据库开发人员能够编写数据库应用程序。
- JDBC需要连接驱动（也是类库），驱动是两个设备要进行通信，满足一定通信数据格式，数据格式由设备提供商规定。设备提供商为设备提供驱动软件，通过软件可以与该设备进行通信。我们使用的是mysql的驱动mysql-connector-java-5.1.39-bin.jar

 总结：

- JDBC是java提供给开发人员的一套操作数据库的接口
- 数据库驱动就是实现该接口的实现类

## junit安装

一：安装

- plugin安装junit

- plugins这里 安装JUnitGenerator V2.0。
- 修改JUnitGenerator V2.0的配置： Default Template选择JUnit 4。 

二：生成JUnit4测试用例。

　　方法一：在待编写测试的java类源码块上按快捷键Alt + Insert。选择JUnit Test->JUnit 4。

​	方法二：在待编写测试的java类源码块上按快捷键Ctrl + Shift + T。

​	方法三：也可以通过code - generate生成

###  jdbc开发步骤

### 1.步骤介绍

​	1.注册驱动
		告知JVM使用的是哪一个数据库的驱动
	2.获得连接
		使用JDBC中的类,完成对MySQL数据库的连接
	3.获得语句执行平台
		通过连接对象获取对SQL语句的执行者对象
	4.执行sql语句
		使用执行者对象,向数据库执行SQL语句
		获取到数据库的执行后的结果
	5.处理结果
	6.释放资源  一堆close()

### 2.步骤说明

1. 注册数据库驱动程序

```
//最新版的驱动器改了，导入这个cj包后，不用注册驱动程序了
import com.mysql.cj.jdbc.Driver;
```

2. 获取数据库的连接对象

```
public class JDBCDemo {
	public static void main(String[] args)throws ClassNotFoundException,SQLException{	
	//2.获得数据库连接  DriverManager类中静态方法
	//static Connection getConnection(String url, String user, String password)。返回值是Connection接口的实现类,在mysql驱动程序
	//url: 数据库地址  jdbc:mysql://连接主机IP:端口号//数据库名字??serverTimezone=GMT&useSSL=false
	String url = "jdbc:mysql://localhost:3296/mybase";
	//用户名和密码用自己的
	String username="root";
	String password="123";
	Connection con = DriverManager.getConnection(url, username, password);
	System.out.println(con);					
}
}
```

3. 获取SQL语句的执行对象

```
public class JDBCDemo {
	public static void main(String[] args)throws ClassNotFoundException,SQLException{
	//3.获得语句执行平台, 通过数据库连接对象,获取到SQL语句的执行者对象
	// con对象调用方法  Statement createStatement() 获取Statement对象,将SQL语句发送到数据库。返回值是 Statement接口的实现类对象,,在mysql驱动程序
	Statement stat = con.createStatement();
	System.out.println(stat);
	}
}
```

4.执行update语句，只能返回int值，即成功修改了的记录条数：insert delete update。

```
// 通过执行者对象调用方法执行SQL语句,获取结果
// int executeUpdate(String sql)  执行数据库中的SQL语句, insert delete update
// 返回值int,操作成功数据表多少行
	int row = stat.executeUpdate("INSERT INTO sort(sname,sprice,sdesc) VALUES('汽车用品',50000,'疯狂涨价')");
	System.out.println(row);
				
	//6.释放资源  一堆close()
	stat.close();
	con.close();
```

执行select语句：

```java
// 拼写查询的SQL
String sql = "SELECT * FROM sort";

//4. 调用执行者对象方法,执行SQL语句获取结果集
// ResultSet executeQuery(String sql)  执行SQL语句中的select查询
// 返回值ResultSet接口的实现类对象,实现类在mysql驱动中
ResultSet rs = stat.executeQuery(sql);

//5 .处理结果集
// ResultSet接口方法 boolean next() 返回true,有结果集,返回false没有结果集
while(rs.next()){
    //获取每列数据,使用是ResultSet接口的方法 getXX方法参数中可以是列编号（从1开始），但建议写列名
    System.out.println(rs.getInt("sid")+"   "+rs.getString("sname")+"   "+rs.getDouble("sprice")+"   "+rs.getString("sdesc"));
}

```

5.释放资源

```
//6.释放资源  一堆close()
	rs.close();
	stat.close();
	con.close();
```

### 3.预编译方式注入

PrepareStatement接口预编译SQL语句
 a: 预处理对象

//使用PreparedStatement预处理对象时，建议每条sql语句所有的实际参数，都使用逗号分隔。
String sql = "insert into sort(sid,sname) values(?,?)";;
//PreparedStatement预处理对象代码：
PreparedStatement psmt = conn.prepareStatement(sql)

b: 执行SQL语句的方法介绍
	int executeUpdate(); --执行insert update delete语句.
	ResultSet executeQuery(); --执行select语句.
	boolean execute(); --执行select返回true 执行其他的语句返回false.
c: 设置实际参数
	void setXxx(int index, Xxx xx) 将指定参数设置为给定Java的xx值。在将此值发送到数据库时，驱动程序将它转换成一个 SQL Xxx类型值。
	例如：
	setString(2, "家用电器") 把SQL语句中第2个位置的占位符？ 替换成实际参数 "家用电器"

```java
public void f1() throws Exception{
    String url = "jdbc:mysql://localhost:3306/day?serverTimezone=GMT&useSSL=false";
    String username = "root";
    String password = "root";
    Connection connection = DriverManager.getConnection(url, username, password);
    String sql = "select * from orders";
    PreparedStatement statement = connection.prepareStatement(sql);
    ResultSet rs = statement.executeQuery();
    while (rs.next()){
        System.out.println(rs.getString("id")+"  "+rs.getString("price")+"  "+rs.getString("user_id"));
    }
    rs.close();
    statement.close();
    connection.close();
}
```

### 4.构建工具类

```java
public class JDBCUtils {
    private JDBCUtils(){}
    private static Connection con ;
    
    static{
        try{
            Class.forName("com.mysql.jdbc.Driver");
            String url = "jdbc:mysql://localhost:3296/mybase";
            String username="root";
            String password="123";
            con = DriverManager.getConnection(url, username, password);
        }catch(Exception ex){
            throw new RuntimeException(ex+"数据库连接失败");
        }
    }

    /*
	* 定义静态方法,返回数据库的连接对象
	 */
    public static Connection getConnection(){
        return con;
    }
    public static void close(Connection con,Statement stat){
        if(stat!=null){
            try{
                stat.close();
            }catch(SQLException ex){}
        }
        if(con!=null){
            try{
                con.close();
            }catch(SQLException ex){}
        }
    }
    // 构成重载，因为有的时候操作是uodate，就没有rs
     public static void close(Connection con,Statement stat, ResultSet rs){
	   if(rs!=null){
            try{
                stat.close();
            }catch(SQLException ex){}
        }
        if(stat!=null){
            try{
                stat.close();
            }catch(SQLException ex){}
        }
        if(con!=null){
            try{
                con.close();
            }catch(SQLException ex){}
        }
    }
}
```

将数据库相关的配置写在conf.properties文件中。由反射知：类的加载器会在存放class文件的bin目录中找到class文件进行类的加载，所以把配置文件也放在bin目录，就肯定能找到配置文件。

InputStream in = PropertiesDemo.class.getClassLoader().getResourceAsStream(xxx.properties)

# DBUtils

## 介绍

a：说明

DBUtils是java编程中的数据库操作实用工具，小巧简单实用。
DBUtils封装了对JDBC的操作，简化了JDBC操作，可以少写代码。需要项目导入commons-dbutils-1.6.jar才能够正常使用DBUtils工具。
b：Dbutils三个核心功能介绍

- QueryRunner中提供对sql语句操作的API. 

  	queryrunner作用:操作sql语句
  	1.构造方法:构造器（使用下面的构造器就会底层帮我们创建连接,创建语句执行者,释放资源.就不需要DbUtils了）
  		new QueryRunner(Datasource ds);
  	2.编写sql，使用预编译语句
  	3.执行sql
  		query()  update()
  	update的返回值是int，代表成功影响的条数。如果row>1则程序正常

- ResultSetHandler接口，用于定义select操作后，怎样封装结果集.

- DbUtils类，它就是一个工具类,定义了关闭资源与事务处理的方法

  `Dbutils.closeQuitely（conn） : 就是关闭的时候处理了异常`

  `commitAndClose(Connection conn):提交事务并释放连接`

## javaBean类

a: 概念
JavaBean就是一个类，在开发中常用封装数据。具有如下特性

1. 需要实现接口：java.io.Serializable ，通常实现接口这步骤省略了，不会影响程序。
2. 提供私有字段：private 类型 字段名;
3. 提供getter/setter方法：
4. 提供无参构造

## ResultSetHandler（接口）结果集的处理实现类

| ArrayHandler      | 将结果集中的第一条记录封装到一个Object[]数组中，数组中的每一个元素就是这条记录中的每一个字段的值 |
| ----------------- | ------------------------------------------------------------ |
| ArrayListHandler  | 将结果集中的每一条记录都封装到一个Object[]数组中，将这些数组在封装到List集合中。 |
| ★★BeanHandler     | 将结果集中第一条记录封装到一个指定的javaBean中。             |
| ★★BeanListHandler | 将结果集中每一条记录封装到指定的javaBean中，将这些javaBean在封装到List集合中 |
| ColumnListHandler | 将结果集中指定的列的字段值，封装到一个List集合中             |
| ★ScalarHandler    | 它是用于单数据。例如select count(*) from 表操作。            |
| MapHandler        | 将结果集第一行封装到Map集合中,Key 列名, Value 该列数据       |
| ★MapListHandler   | 将结果集第一行封装到Map集合中,Key 列名, Value 该列数据,Map集合存储到List集合 |

调用QueryRunner类方法query(Connection con,String sql,ResultSetHandler r, Object..params)

- BeanHandler的使用：

//调用方法,传递结果集实现类BeanHandler。BeanHandler(Class<T> type) 
`Sort s = qr.query(con, sql, new BeanHandler<Sort>(Sort.class));`

没有结果就返回Null

- MapHandler

Map<键,值> 键:列名(肯定是string类型)      值:这列的数据，不一定什么类型

`Map<String,Object> map = qr.query(con, sql, new MapHandler());`

# 连接池

- 使用jdbc的时候,每操作一次都需要获取连接(创建)用完之后把连接释放掉了(销毁),通过连接池来优化curd操作.

## 连接池概述:管理数据库的连接

- 作用：提高项目的性能。就是在连接池初始化的时候存入一定数量的连接,用的时候通过方法获取,不用的时候归还连接即可.
- 规范：所有的连接池必须实现一个接口 javax.sql.DataSource接口
- 获取连接方法：Connection getConnection() 
  归还连接的方法：就是以前的释放资源的方法.调用connection.close();

## 自定义连接池

自定义一个连接池

```java
public class MyDataSource {
	static LinkedList<Connection> pool=new LinkedList<>();
	static{
		//初始化的时候 需要放入3个连接
		for (int i = 0; i < 3; i++) {
			try {
				Connection conn = JdbcUtils.getConnection();
				pool.addLast(conn);
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

	//从连接池中获取连接
	public static Connection getConnection(){
		//获取连接的时候需要判断list是否为空
		if(pool.isEmpty()){
			//在添加2个连接进去
			for (int i = 0; i < 3; i++) {
				try {
					Connection conn = JdbcUtils.getConnection();
					pool.addLast(conn);
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
		System.out.println("从池中获取一个连接");
		 Connection conn = pool.removeFirst();
		 //将conn进行包装 
		 // ConnectionWarp myConn = new ConnectionWarp(conn);
		 ConnectionWarp myConn = new ConnectionWarp(conn,pool);
		return myConn;
	}
	
	//归还连接的方法
	public static void addBack(Connection conn){
		//将conn放入到list的最后面即可
		pool.addLast(conn);
        System.out.println("连接已归还");
	}
}
```

### 问题

1.其中有一个addBack方法，跟close方法不能很好的区分。最好只有一个close方法，即调用原来的close方法时实现归还连接池，而不是销毁。

2.创建连接池的时候能不能面向接口编程.

3.额外增加连接池的方法,那么程序员需要记住这些方法.能不能不额外去提供一些方法.

***** 解决：就是要去增强Connection的close方法.

​       1.继承的方法:

​            * 继承的使用条件：能够控制这个类的构造.

​       2.装饰者模式:(★★★)

​            * 装饰者模式的使用条件：

​               1.装饰者和被装饰者实现同一个接口或者继承同一个类
		2.装饰者中要有被装饰者的引用
		3.对需要增强的方法进行加强
		4.对不需要加强的方法调用原来方法

​            * 接口中方法过多,只增强其中的一个方法.其他方法都需要原样调用原有方法.

​       3.动态代理:(*****)

​            * JDK的动态代理使用条件：

​	    * 被代理的对象必须实现接口.

### 装饰者模式(★★★)：

装饰者模式的使用条件：

​       1.装饰者和被装饰者实现同一个接口或者继承同一个类
       2.装饰者中要有被装饰者的引用
	3.对需要增强的方法进行加强
	4.对不需要加强的方法调用原来方法

实现接口，要实现其所有方法

```java
public class ConnectionWarp implements Connection {

    private Connection conn;
    private LinkedList<Connection> list;

    public ConnectionWarp(Connection conn){
        this.conn=conn;
    }

    public ConnectionWarp(Connection conn,LinkedList<Connection> list){
        this.conn=conn;
        this.list=list;
    }

    @Override
    //不需要加强的方法 调用原来的
    public Statement createStatement() throws SQLException {
        // TODO Auto-generated method stub
        return conn.createStatement();
    }

    @Override
    //不需要加强的方法 调用原来的
    public PreparedStatement prepareStatement(String sql) throws SQLException {
        // TODO Auto-generated method stub
        return conn.prepareStatement(sql);
    }

    @Override
    //需要加强的方法
    public void close() throws SQLException {
        //添加到连接池中
        System.out.println("前:"+list.size());
        //System.out.println(this);//this代表的是当前连接
        list.addLast(this);
        System.out.println("后:"+list.size());

        System.out.println("已经归还到连接池中");
    }
}
```

## 开源连接池：DBCP

核心API：BasicDataSource，BasicDataSourceFactory

### BasicDataSource类的使用

```java
/*
*  连接池jar包中,定义好一个类 BasicDataSource
*  实现类数据源的规范接口 javax.sql.DataSource
*/
public class DataSoruceDemo {
	public static void main(String[] args) {
	//创建DataSource接口的实现类对象
    //实现类, org.apache.commons.dbcp
	BasicDataSource dataSource = new BasicDataSource();

	//连接数据库的4个最基本信息,通过对象方法setXXX设置进来
	dataSource.setDriverClassName("com.mysql.jdbc.Driver");
	dataSource.setUrl("jdbc:mysql://localhost:3306/mybase");
	dataSource.setUsername("root");
	dataSource.setPassword("123");

    //在catch中抛出运行时异常，连接出错直接退出
	try {
	//调用对象方法getConnection获取数据库的连接
		Connection con = dataSource.getConnection();
		System.out.println(con);
	}catch(SQLException ex){
	//			System.out.println(ex);
	// 先打印出出错原因，便于工程师分析。
        ex.printStackTrace();
		throw new RuntimeException("数据库连接失败");
	}
	}
}
```

### BasicDataSource类的常见配置

​			分类	属性			描述
			必须项	
					driverClassName	数据库驱动名称
					url				数据库的地址
					username		用户名
					password		密码
			基本项（扩展）	
					maxActive		最大连接数量
					minIdle			最小空闲连接
					maxIdle 		最大空闲连接
					initialSize		初始化连接

### 实现数据库连接池工具类

/*

使用DBCP实现数据库的连接池

连接池配置,自定义类,

最基本四项完整

对于数据库连接池其他配置,自定义
*/

```java
import javax.sql.DataSource;

import org.apache.commons.dbcp.BasicDataSource;
public class JDBCUtils{
    //创建出BasicDataSource类对象
    private static BasicDataSource datasource = new BasicDataSource();

    //静态代码块,对象BasicDataSource对象中的配置,自定义
    static{
        //数据库连接信息,必须的
        datasource.setDriverClassName("com.mysql.jdbc.Driver");
    datasource.setUrl("jdbc:mysql://localhost:3306/day33_user");
        datasource.setUsername("root");
        datasource.setPassword("123");
        //对象连接池中的连接数量配置,可选的
        datasource.setInitialSize(10);//初始化的连接数
        datasource.setMaxActive(8);//最大连接数量
        datasource.setMaxIdle(5);//最大空闲数
        datasource.setMinIdle(1);//最小空闲
    }
    //定义静态方法,返回BasicDataSource类的对象
    public static DataSource getDataSource(){
        return datasource;
    }
}
```

## 开源连接池：C3P0

### C3P0:(★)

hibernate和spring使用
有自动回收空闲连接的功能.
使用步骤:
	1.导入jar包(c3p0-0.9.1.2.jar)
	2.使用api
		a.硬编码(不推荐)
			new ComboPooledDataSource()
		b.配置文件
			配置文件的名称:c3p0.properties 或者 c3p0-config.xml
			配置文件的路径:src下	

	编码只需要一句话
	new ComboPooledDataSource()//使用默认的配置
	new ComboPooledDataSource(String configName)//使用命名的配置（在配置文件中，有多个配置，每个配置起名字） 若配置的名字找不到,使用默认的配置

### 插入示例

```java
public class C3p0Demo {
    @Test
   public void insert() throws SQLException{
		//1.创建queryrunner类
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
       
		//2.编写sql
		String sql="insert into category values(?,?)";
		
		//3.执行sql,自动进行资源回收释放
		qr.update(sql, "c201","厨房电器");
	}
}
```

### 查询示例

```java
public class ResultHandleDemo {
    @Test
    public void arrayHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select * from category";
        Object[] query = qr.query(sql, new ArrayHandler());
        System.out.println(Arrays.toString(query));
    }

    @Test
    public void arrayListHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select * from category";
        List<Object[]> list = qr.query(sql, new ArrayListHandler());
        for (Object[] obj : list) {
            System.out.println(Arrays.toString(obj));
        }
    }

    @Test
    public void beanHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select * from category";
        Category bean = qr.query(sql, new BeanHandler<>(Category.class));
        System.out.println(bean);
    }

    @Test
    public void beanListHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select * from category";
        List<Category> list = qr.query(sql, new BeanListHandler<>(Category.class));
        for (Category bean : list) {
            System.out.println(bean);
        }
    }

    @Test
    public void mapHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select * from category";
        Map<String, Object> map = qr.query(sql, new MapHandler());
        System.out.println(map);
    }

    @Test
    public void mapListHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select * from category";
        List<Map<String, Object>> list = qr.query(sql, new MapListHandler());
        for (Map<String, Object> map : list) {
            System.out.println(map);
        }
    }

    @Test
    public void scalarHandler() throws SQLException{
        QueryRunner qr=new QueryRunner(DataSourceUtils.getDataSource());
        String sql="select count(*) from category";
        Object obj = qr.query(sql, new ScalarHandler());
        System.out.println(obj.getClass().getName());
    }
}
```

