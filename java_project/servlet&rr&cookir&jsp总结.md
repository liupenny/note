# servlet&rr&cookir&jsp

# servlet

```
	会创建一个servlet
	会更改web.xml
	servletContext:
		全局管理者 上下文
			资源共享(域对象)
			获取资源文件
				getRealPath()
				getResourceAsStream()
			获取mimetype
				getMimeType()
	面试题:
		serlvet的生命周期
```

# rr

	response:往浏览器写东西
			重定向:response.sendRedirect(绝对路径)
			定时刷新:refresh
				setHeader("refresh","秒数;url=跳转路径")
				meta中也可以设置刷新
			getWriter():字符流，自己打印信息用
			getOutputStream():字节流
			文件下载:
				setContentType(mime类型)
				setHeader("content-disposition","attachment;filename="+文件名);
	
	request:获取浏览器发送过来的数据
		获取参数:3个
		中文乱码:
			通用的方式
				new String(名字.getBytes("iso-8859-1"),"utf-8");
			针对post请求
				request.setCharacterEncoding("utf-8");
		请求转发(域对象)
			request.getRequestDispatcher(内部路径).forward(..);

# cookie和session

	cookie:浏览器端会话技术
			常用方法:
				new Cookie(String key,String value);
				写回浏览器
					response.addCookie(Cookie c)
				获取cookie
					request.getCookies()
				cookie的api
					getName()
					getValue()
					setMaxAge(int 秒)
					setPath(String path)
	session:服务器端的会话技术
		常用方法:
			获取session
				request.getSession
		用域对象保存:私有的数据（用户名，验证码）。使用xxxAttribute方法
			创建:第一次调用req.getsession获取
			销毁:
				服务器非正常关闭
				session超时
				手动删除:
					session.invalidate()

# jsp el jstl

	jsp的指令 include taglib
		jsp的内置对象 面试题
		jsp:forward
		jsp:include
	
	el:全部
		获取数据
		执行运算
		
	jstl:
		if
		foreach