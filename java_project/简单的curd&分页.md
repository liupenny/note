# 简单的curd&分页

# 案例1-展示所有商品

- 需求:

​	点击首页上的超链接,在页面上展示所有的商品信息

- 步骤分析:

```
1.数据库和表
2.新建一个项目
3.导入jar包
    驱动 dbutils c3p0 jstl beanutils
4.包结构
    utils工具类:datasourceutils
    导入c3p0配置文件
5.新建一个首页index.jsp	
    添加一个超链接
6.点击超链接 发送一个servlet上(FindAllServlet)
7.FindAllServlet:
    调用service,查询所有的商品 返回值:list
    将list放入request域中,请求转发 prodouct_delall_list.jsp
```

# 案例2-添加商品

- 需求:

​	在index.jsp添加一个超链接,跳转到一个页面,用来填写商品信息,点击保存按钮,将商品保存到数据库中

- 步骤分析:

```
1.在index.jsp添加一个超链接 
    跳转到add.jsp
2.add.jsp放入一个表单
3.表单提交到 AddProductServlet
    封装数据
    调用service完成保存操作
    跳转到FindAllServlet（request域中有数据就只能用请求转发，要访问站外资源就只能重定向。其他的两者都行）
    (请求转发和重定向)
```

- 有表单使用的时候若使用请求转发会出现重复提交

```
方案1:重定向
方案2:令牌机制
扩展:令牌机制
    在添加页面上随机生成一个字符串,
        放入session中一份,放入表单中一份
        提交的时候在后台获取两个码
        处理完第一次提交后，然后移除session中码(只使用一次)
        然后判断两个码是否一致,
        再次提交时判断，若不一致就是重复提交了
```

```java
public class AddProductServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //0.设置编码
        request.setCharacterEncoding("utf-8");

        //扩展 令牌机制
        //0.1 获取session中令牌和提交过来的令牌
        String r_lingpai = request.getParameter("r_lingpai");
        String s_lingpai = (String) request.getSession().getAttribute("s_lingpai");

        //0.2 移除session中的令牌
        request.getSession().removeAttribute("s_lingpai");

        //0.3 比较两个令牌
        if(s_lingpai==null || !s_lingpai.equals(r_lingpai)){
            //已经提交过了 生成错误提示信息 放入request域中, 到msg.jsp
            request.setAttribute("msg", "商品已经保存!");
            request.getRequestDispatcher("/msg.jsp").forward(request, response);
            return;
        }

        //1.封装数据
        Product p=new Product();
        try {
            BeanUtils.populate(p, request.getParameterMap());

            //1.1设置pid
            p.setPid(UUIDUtils.getId());
            //1.2设置时间
            p.setPdate(new Date());
            //2.调用service完成添加操作
            new ProductService().addProuct(p);
            //3.页面跳转 findAll
            //先用请求转发
            request.getRequestDispatcher("/findAll").forward(request, response);

        }  catch (Exception e) {
            e.printStackTrace();
            request.setAttribute("msg", "添加商品失败");
            request.getRequestDispatcher("/msg.jsp").forward(request, response);
        }
    }
}
```

# 案例3-修改商品信息:

- 需求:

​	在product_delall_list.jsp每一个商品后面都有修改操作.点击修改操作,到了一个edit.jsp(将原来的数据展示出来。之所以能展示是因为先查询了商品信息),最后保存就可以了

- 步骤分析:先查询后修改

```
	查询步骤分析:
		点击修改连接的时候
			<a href="/day14/getProductById?pid=sss">修改</a>
		getProductById
			获取pid
			通过pid获取到商品 返回值product
			将product放入request域中 请求转发到edit.jsp
```

```
	修改步骤分析:
		edit.jsp已经将商品的所有信息展示出来
			需要将商品的id通过隐藏域放入表单中
			点击保存,跳转到editProductServlet
		editProductServlet:
			1.封装数据
			2.调用servcie完成修改更新操作
			3.页面跳转到 FindAllServlet(重定向)
```

# 案例4-删除商品

- 需求:

​	在列表页面上,点击一个商品的删除操作,弹出一个提示,点击确定的时候,商品当前商品

- 步骤分析:

```
	先给删除添加事件
		单击事件 弹出提示
			confirm()
		点击确定再去删除商品
			location.href="/day14/deleteProductById?pid=xxx" 相当于超链接
	deleteProductById
		1.获取商品id
		2.调用service完成删除操作
		3.页面重定向 FindAllServlet
```

## 扩展:删除多个商品:

- 需求:

​	在每个商品前面添加复选框,勾选需要删除的商品,添加一个按钮(删除选中),点击之后删除选中的商品

- 步骤分析:

```
	给每一个商品添加复选框(全选全不选),同时添加name属性 值为"pid",value为当前商品的pid
	点击删除选中,需要将勾选上的商品的id提交到后台
		request.getParameterValues("pid")
		必须把所有的商品的放入一个表单中,
			需要在按钮添加事件 
				需要先获取表单,
				调用表单的submit()
```

	delCheckedServlet
		1.获取所有要删除的商品的id      String[] ids
		2.调用servcie完成操作
		3.页面重定向 FindAllServlet

# 扩展:案例5-多条件查询 

- 需求:

​	在product_list.jsp页面上添加一个表单,输入商品名称 和 关键词,点击确定,将符合条件的商品展示在当前页面上

- 步骤分析:

```
	在product_list.jsp页面上添加一个表单,添加一个查询按钮
	提交的路径 findProductByCondition
		1.获取两个条件
		2.调用service完成查询 返回值:list
		3.将list放入request域中,请求转发
	productDao
		基本sql:select * from product where 1=1
		若商品名称不为空 and pname like ...
		若商品名不为空 and pdesc like ...
```

# 案例6-分页展示商品

- 需求:

​	将商品进行分页展示

- 技术分析:

​	分页

## 分页:

​	将数据按照页码划分,提高用户的体验度.

- 分类:

​	物理分页: **(开发中经常使用)**
		一次只去数据库中查询当前页需要的数据.
	逻辑分页:
		一次性将所有数据查询出来,放入内存(集合),每次查询只需要去内存中截取.(因为如果1w个商品，修改了一个还要重新查询，很麻烦。)

- mysql中分页:	limit 

```
格式1:
    select ....  limit m,n;
    从索引为m条开始向后查找n条数据
    就是从第m+1条 到 第m+n条  []
格式2:
    select .... limit n;
    等价于:select .... limit 0,n; （查一个满足条件的数据这种情况下有效率）
		
举例：
	每页显示3条数据 
	第一页 		limit 0,3
	第二页 		limit 3,3
	第三页 		limit 6,3
	第n页 		limit (n-1)*3,3

扩展:
	oracle中 rownum （用两条语句嵌套出来的）
	sqlserver中 top
```

- 每一页需要的数据有

```
当前页内容	limit查询 
当前页码    从前台传递过去
每页显示的条数 固定
总条数		count(*)
总页数		总条数/每页显示的条数 (int)Math.ceil(double)
```

​	以后开发中一般会将这5个参数封装一个javabean(PageBean)

```
private List<T> list;//当前页内容	 	查询
private int currPage;//当前页码 	 	传递
private int pageSize;//每页显示的条数	固定
private int totalCount;//总条数			查询
private int totalPage;//总页数			计算
```

- 步骤分析:

```
最终结果:
    [首页][上一页][下一页][尾页]
    [首页][上一页]x x x[下一页][尾页]
```

- 后端页面

	1.创建一个页面 product_page.jsp
		有上面的内容
		还需要有当前页的数据
	2.在首页上有一个超链接,点击超链接将第一页查询出来
		<a href="/day14/showProductsByPage?currPage=1">分页展示商品</a>
	3.showProductsByPage
		获取第几页
		调用service 完成查询操作,返回值:pagebean
		将pagebean放入request域中,请求转发product_page.jsp
	4.service.showProductsByPage
		返回值pagebean
		查询当前页的数据
		查询出总条数

1. ProductDao

```
/**
	 * 查询第几页数据
	 * @param currPage
	 * @param pageSize
	 * @return
	 * @throws SQLException 
	 */
	public List<Product> findProductByPage(int currPage, int pageSize) throws SQLException {
		QueryRunner qr = new QueryRunner(DataSourceUtils.getDataSource());
		String sql="select * from product limit ?,?";
		return qr.query(sql, new BeanListHandler<>(Product.class), (currPage-1)*pageSize,pageSize);
	}
```

2. ProductService

```
/**
	 * 分页查询商品
	 * @param currPage 第几页
	 * @param pageSize 每页显示的条数
	 * @return pagebean
	 * @throws SQLException 
	 */
	public PageBean<Product> showProductsByPage(int currPage, int pageSize) throws SQLException {
		//查询当前页数据 limit (当前页-1)*每页显示条数,每页显示条数;
		ProductDao dao=new ProductDao();
		List<Product> list=dao.findProductByPage(currPage,pageSize);
		
		//查询总条数
		int totalCount=dao.getCount();
		
		return new PageBean<>(list, currPage, pageSize, totalCount);
	}

```

- 前端页面

```
在页面上展示
	1.展示当前数据 
		通过<c:forEach items="${pb.list}">
	2.添加首页 上一页 ....的超链接
	3.判断是否是第一页.若是第一页 不展示首页和上一页
	4.判断是否是最后一页 若是最后一页 不展示尾页和下一页
	5.展示所有页码
		<c:forEach begin='1' end="${pb.totalPage}">
	6.判断是否是当前页
		若是当前页 不加超链接
		若不是当前页 添加超链接
```

```html
<body>
    <table border="1" align="center" width="88%">
        <tr>
            <th>pid</th>
            <th>商品图片</th>
            <th>商品名称</th>
            <th>市场价</th>
            <th>商城价</th>
            <th>商品描述</th>

        </tr>
        <c:forEach items="${pb.list }" var="p">
            <tr>
                <td width='8%'>${p.pid }</td>
                <td width='8%'><img alt=""
                                    src="${pageContext.request.contextPath }/${p.pimage}" width="80"></td>
                <td width='8%'>${p.pname }</td>
                <td width='8%'>${p.market_price }</td>
                <td width='8%'>${p.shop_price }</td>
                <td>${p.pdesc }</td>

            </tr>
        </c:forEach>
    </table>
    <center>
        <!-- 若是第一页 首页和上一页不展示 -->
        <c:if test="${pb.currPage!=1 }">
            <a href='${pageContext.request.contextPath}/showProductsByPage?currPage=1'>[首页]  </a>
            <a href='${pageContext.request.contextPath}/showProductsByPage?currPage=${pb.currPage-1}'>[上一页]</a>
        </c:if>

        <!-- 将所有的页码展示出来 -->
        <c:forEach begin="1" end="${pb.totalPage }" var="n">
            <!-- 若是当前页 不可点 -->
            <c:if test="${pb.currPage == n }">
                ${n }
            </c:if>

            <!-- 若不是当前页 可点 -->
            <c:if test="${pb.currPage != n }">
                <a href="${pageContext.request.contextPath}/showProductsByPage?currPage=${n}">${n }</a>
            </c:if>
        </c:forEach>

        <!-- 若是最后一页 尾页和下一页不展示 -->
        <c:if test="${pb.currPage!=pb.totalPage }">
            <a href='${pageContext.request.contextPath}/showProductsByPage?currPage=${pb.currPage+1}'>[下一页]</a>
            <a href='${pageContext.request.contextPath}/showProductsByPage?currPage=${pb.totalPage}'>[尾页]  </a>
        </c:if>
        第${pb.currPage }页/共${pb.totalPage }页
    </center>
</body>
</html>
```



- 扩展:页面多的时候 采用前五后四的方式

1. ​	只需要控制begin和end
2. ​	begin 判断当前页-5>0?当前页-5:1
3. ​	end 判断 当前页+4>总页数?总页数:当前页+4