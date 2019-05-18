# nginx

# 发布项目

1. 数据库和表	

```
备份store28的数据库
    在windows下
        mysqldump -uroot -p1234 store28 > g:/1.sql
    将1.sql上传 root目录
    通过远程工具还原数据库(都需要手动创建数据库)
        先登录mysql
        创建数据库 store28
        进入store28
            source /root/1.sql

```

2. 准备项目

	将项目打包 war(war包和zip包是一样的，可以先压缩成zip包，改后缀为war)
	
	war包的特点:
	    在tomcat/webapps目录下 只要tomcat启动 war会自动解压(linux 上可用jar -xvf命令解压)
	将store.war上传到虚拟机的root目录下
	将store.war移动到tomcat/webapps下即可
# nginx:

​	Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器

- 特点:

​	反向代理 负载均衡 动静分离...

1. 反向代理 :

-  先来了解正向代理:需要我们用户,手动的设置代理服务器的ip和端口号
- 反向代理:代理服务器的,用户不需要设置.

2. 负载均衡:

​	原理就是数据流量分摊到多个服务器上执行，减轻每台服务器的压力，多台服务器共同完成工作任务，从而提高了数据的吞吐量。

3. 动静分离:

​	将静态的资源放到反向服务器,节省用户的访问时间.

## 用nginx在window上搭建一个集群

1. 在g盘新建两个目录 tomcat1 tomcat2
2. 修改tomcat2的端口 在tomcat1的端口上+10
3. 解压nginx

```
修改nginx的 nginx.conf文件
在locatioin / 下添加了反向代理
    proxy_pass 代理服务器
这是只是代理一台服务器
```

4. 代理集群

```
需要在http节点上添加一个
    upstream servlet_yujia{
        server 127.0.0.1:8080;
        server 127.0.0.1:8090;
    }
修改location /下的反向代理 
    proxy_pass http://servlet_yujia
```

5. session共享问题

- 解决方式1:只能在window下好使

```
web服务器解决(广播机制)
    注意:tomcat下性能低
    修改两个地方:
        1.修改tomcat的server.xml 支持共享
            将 引擎标签下的 
                <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>
            注释去掉
        2.修改项目的配置文件 web.xml中添加一个节点
```

- 解决方式2:

​	可以将session的id放入redis中

- 解决方式3:

​	保证一个ip地址永远的访问一台web服务器,就不存在session共享问题了,在linux
在nginx的配置文件中upstream中添加 ip_hash;

## 在linux搭建集群

	1.先将 nginx上传到linux上
	2.解压nginx
	3.先编译nginx
	    安装依赖包
	        yum install gcc-c++
	        yum install -y pcre pcre-devel
	        yum install -y zlib zlib-devel
	        yum install -y openssl openssl-devel
	    执行编译
	        先进入 nginx的目录
	        执行
	            ./configure
	
	4.安装nginx
		执行 
			make
			make install
	5.启动nginx
		cd nginx目录下
			配置文件 conf
			启动nginx 
				./nginx 
	6.将端口号80 放行
		/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
		将该设置添加到防火墙的规则中
		/etc/rc.d/init.d/iptables save
	7.修改conf文件 和window下一样
		配置集群