# 事务&mvc&反射补充

分层和事务

# 案例-使用mvc思想完成转账操作

- 需求:

​	在一个页面上有汇款人 收款人 转账金额.一旦转账之后,汇款人金额减少,收款人金额增多.使用事务控制起来.

- 技术分析:

​	mvc思想
​	事务

## mvc思想演变

```
servlet-->缺点:生成html内容太麻烦
		|
	jsp--->缺点:阅读起来不方便,维护比较困难
		|
	jsp+javabean:
			jsp的model1（设计模式1）:
				jsp:接受请求,展示数据
				javabean:和数据打交道 
		|
	jsp+javabean+servlet
			jsp的model2（设计模式2）:
				jsp:展示数据
				javabean:和数据打交道
				servlet:接受请求,处理业务逻辑
			就是MVC思想的体现
```

## MVC:

```
	就是将业务逻辑,代码,显示相分离的一种思想
	M:model 模型 作用:主要是封装数据,封装对数据的访问
	V:view 	视图 作用:主要是用来展示数据 一般是jsp担任的
	C:ctrl	控制 作用:接受请求,找到相应的javabean完成业务逻辑
```

## jsp设计模式1 model1:(了解)

​	javabean+jsp
​	javabean在model1使用(了解)-通过两种动作标签（将form.jsp中表单中的数据提交给login.jsp，生成javabean）
​	id是给javabean起的名字，class要是全限定名

- form.jsp

```jsp
<form method="post" action="${pageContext.request.contextPath }/model1/login.jsp">
    <table border='1'>
        <tr>
            <td>用户名</td>
            <td><input type="text" name="name"></td>
        </tr>
        <tr>
            <td>密码</td>
            <td><input type="password" name="password"></td>
        </tr>
        <tr>
            <td colspan='2'><input type="submit"></td>
        </tr>
    </table>
</form>
```

- login.jsp:

```jsp
<!-- 接受值 -->
<jsp:useBean id="u" class="com.itheima.domain.User">/jsp:useBean<!--相当于  User u=new User()-->
// property是bean的属性值，name是指给哪个javabean定值
<jsp:setProperty property="name" name="u"/><!--相当于  u.setName(...)-->
<jsp:setProperty property="password" name="u"/>

<!-- 打印值-->
<jsp:getProperty property="name" name="u"/>
```
## 反射:

	1.获取class对象
	    方式1:
	        Class clazz=Class.forName("全限定名")
	    方式2:
	        Class clazz=类名.class;
	    方式3:
	        Class clazz=对象.getClass;
	
	2.可以获取对应类的构造方法(了解)
	    Constructor con = clazz.getConstructor(Class .. paramClass);
	    Person p = (Person) con.newInstance(参数);
	
	3.可以通过clazz创建一个对象(了解)
	    clazz.newInstance();//相当于调用的无参构造器
	
	4.可以通过clazz获取所有的字段 getFiled()(了解中的了解)
	5.可以通过clazz获取所有的方法
	    Method m = clazz.getMethod("sleep");//获取公共的方法
	    Method m = clazz.getDeclaredMethod("sleep");//获取任意的方法
	
	注意:若是私有的方法 必须让该方法可以访问
	    m.setAccessible(true);
	    
	6.Method对象的invoke是有返回值,他的返回值就是目标方法执行的返回值
总结:
	有了class对象之后,无所不能.

- Person类

  ```java
  public class Person {
  private String username;
  private String password;
  public String getUsername() {
      return username;
  }
  public void setUsername(String username) {
      this.username = username;
  }
  public String getPassword() {
      return password;
  }
  public void setPassword(String password) {
      this.password = password;
  }
  
  public Person() {
      System.out.println("空参构造器");
  }
      
  public Person(String username, String password) {
      this.username = username;
      this.password = password;
      System.out.println("带参构造器");
  }
  
  public void eat(){
      System.out.println("会吃");
  }
  
  public void eat(String name){
      System.out.println(name+"再吃");
  }
  
  private void sleep(){
      System.out.println("会睡觉");
  }
  
  private String sleep(String name){
      return name+"在睡觉";
  }
  }
  ```

- 反射演示

  ```Java
  public class Demo {
  @Test
  public void f1() throws Exception{
      //1.获取class对象
      Class clazz=Class.forName("com.itheima.domain.Person");
  
      //2.获取构造器(了解)
      //Constructor con = clazz.getConstructor();
      //Person p = (Person) con.newInstance();
  
      //2.1获取带参的构造器(了解)
      Constructor con = clazz.getConstructor(String.class,String.class);
      Person p = (Person) con.newInstance("tom","123");//相当于  new Person("tom","123")
  
      System.out.println(p.getPassword());
  }
  
  @Test
  public void f2() throws Exception{
      //1.获取class对象
      Class clazz=Class.forName("com.itheima.domain.Person");
  
      //2.获取构造器(了解)
      Person p = (Person) clazz.newInstance();
  }
  
  @Test
  //获取私有方法
  public void f3() throws Exception{
      //1.获取class对象
      Class clazz=Class.forName("com.itheima.domain.Person");
  
      Person p = (Person) clazz.newInstance();
      //2.获取方法
      //Method m = clazz.getMethod("sleep");//获取公共的方法
      Method m = clazz.getDeclaredMethod("sleep");//获取任意的方法
  
      //2.1若是私有的方法 必须让该方法可以访问
      m.setAccessible(true);
  
      //执行方法
      m.invoke(p);
  }
  
  @Test
  public void f11() throws Exception{
      //1.获取class对象
      Class clazz=Class.forName("com.itheima.domain.Person");
  
      //2.获取私有的方法
      Method m = clazz.getDeclaredMethod("sleep", String.class);
      //让方法可以访问
      m.setAccessible(true);
  
      //3.让方法执行
      String res=(String) m.invoke(clazz.newInstance(),"侯振");
      System.out.println(res);
  }
  }
  ```

## javabean在model2(mvc思想)中使用

使用BeanUtils:可以看作封装数据一个工具类

```
使用步骤:
	1.导入jar包
	2.使用BeanUtils.populate(Object bean,Map map);
```

通过反射得到Bean的class，map中都是（key,value）形式，即（属性，值）形式。所以可以通过调用class中的set方法，将map中的key对应的value属性设置进去。

## mvc思想在java ee中的体现

	分层:javaee的三层架构
		web
			作用:
				展示数据 ----jsp
				
				-----servlet-------
				接受请求
				找到对应的service,调用方法 完成逻辑操作
				信息生成或者页面跳转
	service 业务层
		作用:
			完成业务操作
			调用dao
	dao(data access object 数据访问对象：访问数据，包装成对象。持久层)
		作用:
			对数据库的curd操作
一般是从：先了解公司项目需求，从业务层开始出发。

## 事务:

​	就是一件完整的事情,包含多个操作单元,这些操作要么全部成功,要么全部失败.

- 例如:转账

​		包含转出操作和转入操作.

### mysql中的事务（要么成功，要么回滚）:

​	mysql中事务默认是自动提交,一条sql语句就是一个事务.（所以不太好，需要关闭一条语句一个，改为多条语句一个事物）

​	扩展:
​		oracle中事务默认是手动的,必须手动提交才可以.

```
 开启手动事务方式
	方式1:关闭自动事务.(了解)这个是临时的，一旦切换窗口，或者关了重新开都会是on。
		set autocommit = off; 
		然后当语句操作完再commit即可。
	方式2:手动开启一个事务.(理解)
		start transaction;-- 开启一个事务
		commit;-- 事务提交
		rollback;-- 事务回滚
	举例：
		完成 aa给bb转500 这个事物;
	  	start transaction;
		update account set money = money - 500 where name='aa';
		update account set money = money + 500 where name='bb';
		commit; / 或者要撤销：rollback
```

### java中的事务(在业务层实现事物):★

	数据库连接Connection接口的api:★
	    setAutoCommit(false);//手动开启事务
	    commit():事务提交
	    rollback():事务回滚
	
	扩展:了解 Savepoint还原点
		void rollback(Savepoint savepoint) :还原到那个还原点
		Savepoint setSavepoint() :设置还原点
## 步骤分析（使用jdbc）:

	1.数据库和表
	2.新建一个项目 day1301
	3.导入jar包和工具类
	    驱动 jdbcUtils
	    c3p0及其配置文件和工具类
	    dbutils
	4.新建一个account.jsp 表单
	5.accountservlet:
	    接受三个参数
	    调用accountservice.account方法完成转账操作
	    打印信息
	6.account方法中:
	    使用jdbc不考虑事务
	    调用dao完成转出操作
	    调用dao完成转入操作
- 原始方法，service中调用dao，在dao中创建connection
  1. service调用dao

```java
public class AccountService {
    /**
	 * 转账
	 * @param fromUser 转出方
	 * @param toUser 转入方
	 * @param money 金额
	 * @throws Exception 
	 */
    public void account(String fromUser, String toUser, String money) throws Exception {
        AccountDao dao = new AccountDao();
        //1.转出
        dao.accountOut(conn,fromUser,money);
        // 这里一但出现异常，数据库中就会有问题
        int i=1/0;
        //2.转入
        dao.accountIn(conn,toUser,money);
    }
}
```

​	2. Dao创建链接，操作数据库。try操作资源，catch捕获异常，finally关闭资源

```java
public class AccountDao {
/**
	 * 转出钱
	 * @param fromUser
	 * @param money
	 * @throws SQLException 
	 */
public void accountOut_(String fromUser, String money) throws SQLException {
    Connection conn=null;
    PreparedStatement st=null;
    ResultSet rs=null;

    try {
        conn=JdbcUtils.getConnection();
        //编写sql
        String sql="update account set money = money - ? where name =?";

        //创建语句执行者
        st=conn.prepareStatement(sql);

        //设置参数
        st.setString(1, money);
        st.setString(2, fromUser);

        //执行sql
        int i = st.executeUpdate();
        //处理
        System.out.println("出:"+i);

    } catch (SQLException e) {
        e.printStackTrace();
        throw e;
    }finally {
        JdbcUtils.closeResource(conn, st, rs);
    }
}

/**
	 * 转入
	 * @param toUser
	 * @param money
	 * @throws SQLException 
	 */
public void accountIn_(String toUser, String money) throws SQLException {
    Connection conn=null;
    PreparedStatement st=null;
    ResultSet rs=null;

    try {
        conn=JdbcUtils.getConnection();
        //编写sql
        String sql="update account set money = money + ? where name =?";

        //创建语句执行者
        st=conn.prepareStatement(sql);

        //设置参数
        st.setString(1, money);
        st.setString(2, toUser);

        //执行sql
        int i = st.executeUpdate();
        //处理
        System.out.println("入:"+i);

    } catch (SQLException e) {
        e.printStackTrace();
        throw e;
    }finally {
        JdbcUtils.closeResource(conn, st, rs);
    }
}
}
```

```
7.dao中
	上面的代码，一旦出现异常(比如在service中，先accountOut转出后，出现一个i/0异常，那就中断了),钱飞了.
	要想避免这事情,必须添加事务,在service添加事务. 
	为了保证所有的操作在一个事务中,必须保证使用的是同一个连接
	在service层我们获取了连接,开启了事务.如何dao层使用此连接呢????
```

- 方法1:向下传递（jdbc操作）。向下传递参数.注意连接应该在service释放(否则若dao层直接释放了链接，service就没法使用；

  service：try开启事务，成功就commint；catch 处理事务的异常，并回滚；finally关闭连接

```java
public class AccountService {
    /**
 * 转账
 * @param fromUser 转出方
 * @param toUser 转入方
 * @param money 金额
 * @throws Exception 
 */
    public void account(String fromUser, String toUser, String money) throws Exception {
        AccountDao dao = new AccountDao();

        Connection conn=null;
        try {
            //0.开启事务
            conn = JdbcUtils.getConnection();
            conn.setAutoCommit(false);

            //1.转出
            dao.accountOut(conn,fromUser,money);

            int i=1/0;

            //2.转入
            dao.accountIn(conn,toUser,money);

            //3.成功了:事务提交
            conn.commit();
        } catch (Exception e) { //因为上面操作不仅有sql异常，还有别的，所以用父类异常。
            e.printStackTrace();
            //没成功：事务回滚
            conn.rollback();
            // 还要把这个异常扔出去给servlet
            throw e;
        } finally {
             // 释放链接
             JdbcUtils.closeConn(conn);
        }
    }
}
```

 DAO层处理操作数据库的一系列问题，代码不怎么变，只是不用处理conn的删除

```java
public class AccountDao {

    /**
	 * 转出钱
	 * @param fromUser
	 * @param money
	 * @throws SQLException 
	 */
    public void accountOut(Connection conn,String fromUser, String money) throws SQLException {
        PreparedStatement st=null;
        ResultSet rs=null;

        try {
            //编写sql
            String sql="update account set money = money - ? where name =?";
            //创建语句执行者
            st=conn.prepareStatement(sql);

            //设置参数
            st.setString(1, money);
            st.setString(2, fromUser);

            //执行sql
            int i = st.executeUpdate();
            //处理
            System.out.println("出:"+i);

        } catch (SQLException e) {
            e.printStackTrace();
            throw e;
        }finally {
            //JdbcUtils.closeResource(conn, st, rs);
            JdbcUtils.closeStatement(st);
        }
    }

    /**
	 * 转入
	 * @param toUser
	 * @param money
	 * @throws SQLException 
	 */
    public void accountIn(Connection conn,String toUser, String money) throws SQLException {
        PreparedStatement st=null;
        ResultSet rs=null;

        try {
            //编写sql
            String sql="update account set money = money + ? where name =?";

            //创建语句执行者
            st=conn.prepareStatement(sql);

            //设置参数
            st.setString(1, money);
            st.setString(2, toUser);

            //执行sql
            int i = st.executeUpdate();
            //处理
            System.out.println("入:"+i);

        } catch (SQLException e) {
            e.printStackTrace();
            throw e;
        }finally {
            //JdbcUtils.closeResource(conn, st, rs);
            JdbcUtils.closeStatement(st);
        }
    }
}
```

conn是dao层的，但是现在在service层获取，并传递给dao。会有代码侵入。想要：在service层开启conn后，不传递，直接在dao层用。可以使用方法2.

- 方法2： 可以在service层将connection对象绑定在当前线程上，

```
方法2:
    可以在service层将connection对象绑定在当前线程上，然后dao层
    jdk中有一个ThreadLocal类,
    ThreadLocal 实例通常是类中的 private static 字段，
    它们希望将状态与某一个线程（例如，用户 ID 或事务 ID）相关联。 
    
ThreadLocal的方法:
构造:
    new ThreadLocal()
绑定：
	set(Object value):将内容和当前线程绑定
	Object get():获取和当前线程绑定的内容
	remove():将当前线程和内容解绑
	
其实内部维护了一个map集合
map.put(当前线程,内容);
map.get(当前线程)
map.remove(当前线程)
```

获取链接，将conn和当前链接绑定。但threadlocal需要用private static修饰，所以封装一下，在创建链接后直接和当前线程绑定。对datasourceutils进行进一步封装。

1. datasourceutils

```java
public class DataSourceUtils {
    private static ComboPooledDataSource ds = new ComboPooledDataSource();
    private static ThreadLocal<Connection> tl = new ThreadLocal<>();

    /**
     * 获取数据源
     * @return 连接池
     */
    public static DataSource getDataSource(){
        return ds;
    }

    /**
     * 获取连接
     * @return 连接
     * @throws SQLException
     */
    public static Connection getConnection() throws SQLException {
        Connection conn = tl.get();
        if (conn == null) {
            // 第一次获取，创建一个链接并和当前线程绑定
            conn = ds.getConnection();
            // 绑定
            tl.set(conn);
        }
        return conn;
    }

    /**
     * 释放资源
     * @param conn 连接
     * @param st   语句执行者
     * @param rs    结果集      
     */
    public static void closeResource(Connection conn, Statement st, ResultSet rs) {
        closeResource(st,rs);
        closeConn(conn);
    }

    /**
    * @Description: 重构
    * @Param: [st, rs]
    * @return: void
    * @Author: PennyLiu
    * @Date: 2018/10/6
    */
    public static void closeResource(Statement st, ResultSet rs) {
        closeResultSet(rs);
        closeStatement(st);
    }

    /**
     * 释放连接
     *
     * @param conn
     *            连接
     */
    public static void closeConn(Connection conn) {
        if (conn != null) {
            try {
                conn.close();
                // 和当前线程解绑
                tl.remove();
            } catch (SQLException e) {
                e.printStackTrace();
            }
            conn = null;
        }

    }

    /**
     * 释放语句执行者
     *
     * @param st
     *            语句执行者
     */
    public static void closeStatement(Statement st) {
        if (st != null) {
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
            st = null;
        }

    }

    /**
     * 释放结果集
     *
     * @param rs
     *            结果集
     */
    public static void closeResultSet(ResultSet rs) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
            rs = null;
        }

    }

    /** 
    * @Description:开启事务
    * @Param:  
    * @return:  
    * @Author: PennyLiu 
    * @Date: 2018/10/6 
    */
    public static void startTransaction() throws SQLException {
        //获取链接，开启事务
        getConnection().setAutoCommit(false);

    }

    /** 
    * @Description: 事物提交需要：提交-释放资源-和线程解绑
    * @Param: [] 
    * @return: void 
    * @Author: PennyLiu 
    * @Date: 2018/10/6 
    */ 
    public static void commitAndClose(){
        try {
            // 获取链接
            Connection conn = getConnection();
            // 提交事务
            conn.commit();
            // 释放资源
            conn.close();
            // 解除绑定
            tl.remove();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    /** 
    * @Description: 事物回滚
    * @Param: [] 
    * @return: void 
    * @Author: PennyLiu 
    * @Date: 2018/10/6 
    */ 
    public static void rollbackAndClose(){
        try {
            // 获取链接
            Connection conn = getConnection();
            // 提交事务
            conn.rollback();
            // 释放资源
            conn.close();
            // 解除绑定
            tl.remove();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### 使用DButils:

```
1.创建queryrunner
2.编写sql
3.执行sql
QueryRunner:
构造:
    new QueryRunner(DataSource ds):自动事务。调用方法不用传入connection，且资源不用手动释放
    new QueryRunner():手动事务，调用方法必须传入connection，需要手动释放资源
    
常用方法:
    update(Connection conn,String sql,Object ... params):执行的cud操作
    query(Connection conn....):执行查询操作
注意:
    一旦使用手动事务,调用update,query方法的时候都（选择默认参数有conn的那个）需要手动传入connection,并且需要手动关闭连接
```

## 事务总结:

### 事务的特性:★★★

- ACID

```
原子性:事务里面的操作单元不可切割,要么全部成功,要么全部失败
一致性（consistency）:事务执行前后,业务状态和其他业务状态保持一致.
隔离性（Isolation）:一个事务执行的时候最好不要受到其他事务的影响
持久性:一旦事务提交或者回滚.这个状态都要持久化到数据库中
```

### 不考虑隔离性会出现的读问题★★

```
脏读:在一个事务中读取到另一个事务没有提交的数据
不可重复读:在一个事务中,两次查询的结果不一致(针对的update操作)
虚读(幻读):在一个事务中,两次查询的结果不一致(针对的insert操作)（mysql中已修复，无法演示）

通过设置数据库的隔离级别来避免上面的问题(理解)
read uncommitted  	读未提交	上面的三个问题都会出现
★read committed  	读已提交	可以避免脏读的发生
★repeatable read		可重复读	可以避免脏读和不可重复读的发生
serializable		串行化		可以避免所有的问题
```

### 各种问题的发生（了解）

- 演示脏读的发生:

    ```
    使用下面的语句将数据库的隔离级别设置成 读未提交
        set session transaction isolation level read uncommitted;
    查看数据库的隔离级别
        select @@tx_isolation;
    脏读（查看到没有提交的数据）举例:
        aa给bb转账，aa开启事务后先转账，但不提交，bb此时能查到已经转了。但aa再rollback。就没转账了。
    ```

- 避免脏读的发生,将隔离级别设置成  读已提交

    ```
    set session transaction isolation level read committed;
    此时aa第一次转完，bb查看发现没转。aa只能commit，bb再查看，发现转了。但bb是在一个transaction中查同一个数据，却查到了不同的结果。就叫不可重复读。
    不可避免不可重复读（在一次事务中，两次查询的结果不一致）的发生.
    ```

- 避免不可重复读的发生 经隔离级别设置成 可重复读

​    `set session transaction isolation level  repeatable read;`
​    这样，在一次事务中查询的结果就一样了。commit之后再查询就能看到了。

- 演示串行化（只允许一个事务进行操作，只有当前事务结束后别的事务才能进来） 可以避免所有的问题

  `    set session transaction isolation level  serializable;`
  类似锁表的操作.

	四种隔离级别的效率
	read uncommitted>read committed>repeatable read>serializable
	四种隔离级别的安全性
	read uncommitted<read committed<repeatable read<serializable
	
	开发中绝对不允许脏读发生.
	mysql中默认级别:repeatable read
	oracle中默认级别:read committed
	
	java中控制隔离级别:(了解)
	Connection的api
	    void setTransactionIsolation(int level) 
	        level是常量

# 反射

## 声明式编程

- **命令式编程**：命令“机器”*如何*去做事情*(how)*，这样不管你想要的*是什么(what)*，它都会按照你的命令实现。
- **声明式编程**：告诉“机器”你想要的*是什么(what)*，让机器想出*如何*去做*(how)*。

### 声明式编程和命令式编程的代码例子

举个简单的例子，假设我们想让一个数组里的数值翻倍。

我们用命令式编程风格实现，像下面这样：

```
var numbers = [1,2,3,4,5]

var doubled = []

for(var i = 0; i < numbers.length; i++) {

  var newNumber = numbers[i] * 2
  doubled.push(newNumber)

}
console.log(doubled) //=> [2,4,6,8,10]
```

我们直接遍历整个数组，取出每个元素，乘以二，然后把翻倍后的值放入新数组，每次都要操作这个*双倍数组*，直到计算完所有元素。

而使用声明式编程方法，我们可以用 `Array.map` 函数，像下面这样：

```
var numbers = [1,2,3,4,5]

var doubled = numbers.map(function(n) {

  return n * 2
})
console.log(doubled) //=> [2,4,6,8,10]
```

`map` 利用当前的数组创建了一个新数组，新数组里的每个元素都是经过了传入`map`的函数(这里是`function(n) { return n*2 }`)的处理。

`map`函数所作的事情是将直接遍历整个数组的过程归纳抽离出来，让我们专注于描述我们想要的*是什么(what)*。注意，我们传入map的是一个纯函数；它不具有任何副作用(不会改变外部状态)，它只是接收一个数字，返回乘以二后的值。

## 基本原理

解决的问题：使用声明的方式来编程 、 可以在运行时修改类(比如：在函数调用前后加日志、函数加事务、权限控制)

举例：

- web编程里，给所有以add开头的方法添加日志

- web后台，给以service结尾的类添加事务

  使用AOP就能读取xml文件的声明，进一步修改方法



