# 第五天：redis解决高并发

hbase, mondb解决大数据。

redis是单线程

- jedis测试

```java
public class TestJedis {

	@Test
	public void testJedis() throws Exception {
		//创建一个jedis对象，需要指定服务的ip和端口号
		Jedis jedis = new Jedis("192.168.25.153", 6379);
		//直接操作数据库
		jedis.set("jedis-key", "1234");
		String result = jedis.get("jedis-key");
		System.out.println(result);
		//关闭jedis
		jedis.close();
	}
```

- jedis使用规范

```java
@Test
	public void testJedisPool() throws Exception {
		//创建一个数据库连接池对象（单例），需要指定服务的ip和端口号
		JedisPool jedisPool = new JedisPool("192.168.25.153", 6379);
		//从连接池中获得连接
		Jedis jedis = jedisPool.getResource();
		//使用Jedis操作数据库（方法级别使用）
		String result = jedis.get("jedis-key");
		System.out.println(result);
		//一定要关闭Jedis连接
		jedis.close();
		//系统关闭前关闭连接池
		jedisPool.close();
	}
```

- 集群版本

```java
@Test
	public void testJedisCluster() throws Exception {
		//创建一个JedisCluster对象，构造参数Set类型，集合中每个元素是HostAndPort类型
		Set<HostAndPort> nodes = new HashSet<>();
		//向集合中添加节点
		nodes.add(new HostAndPort("192.168.25.153", 7001));
		nodes.add(new HostAndPort("192.168.25.153", 7002));
		nodes.add(new HostAndPort("192.168.25.153", 7003));
		nodes.add(new HostAndPort("192.168.25.153", 7004));
		nodes.add(new HostAndPort("192.168.25.153", 7005));
		nodes.add(new HostAndPort("192.168.25.153", 7006));
		JedisCluster jedisCluster = new JedisCluster(nodes);
		//直接使用JedisCluster操作redis，自带连接池。jedisCluster对象可以是单例 的。
		jedisCluster.set("cluster-test", "hello jedis cluster");
		String string = jedisCluster.get("cluster-test");
		System.out.println(string);
		//系统关闭前关闭JedisCluster
		jedisCluster.close();
	}
}
```

# 策略模式

redis单机版和集群版代码不同，如何切换？

接口+实现类。 写两个实现类分别针对单机&集群。

JedisClient是接口，JedisClientPool是单机版的实现，JedisClientCluster是集群版的实现。

- 代码中添加缓存。

  查询的时候先查缓存，再查数据库。

  更新的时候有新数据进来就把缓存对应 的那条删掉，不然还是一直读缓存。

```java
@Override
	public TaotaoResult addContent(TbContent content) {
		//补全pojo的属性
		content.setCreated( new Date());
		content.setUpdated(new Date());
		//插入到内容表
		contentMapper.insert(content);
		//同步缓存
		//删除对应的缓存信息
		jedisClient.hdel(INDEX_CONTENT, content.getCategoryId().toString());
		return TaotaoResult.ok();
	}

@Override
	public List<TbContent> getContentByCid(long cid) {
		//先查询缓存
		//添加缓存不能影响正常业务逻辑
		try {
			//查询缓存
			String json = jedisClient.hget(INDEX_CONTENT, cid + "");
			//查询到结果，把json转换成List返回
			if (StringUtils.isNotBlank(json)) {
				List<TbContent> list = JsonUtils.jsonToList(json, TbContent.class);
				return list;
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
		//缓存中没有命中，需要查询数据库
		TbContentExample example = new TbContentExample();
		Criteria criteria = example.createCriteria();
		//设置查询条件
		criteria.andCategoryIdEqualTo(cid);
		//执行查询
		List<TbContent> list = contentMapper.selectByExample(example);
		//把结果添加到缓存
		try {
			jedisClient.hset(INDEX_CONTENT, cid + "", JsonUtils.objectToJson(list));
		} catch (Exception e) {
			e.printStackTrace();
		}
		//返回结果
		return list;
	}
```

contrib：扩展包。home：配置文件，库