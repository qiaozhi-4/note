# 基本命令

#### 开启服务器

在文件路径上面进入cmd



开启主数据库

```txt
redis-server.exe
```



开启从数据库

```txt
redis-server.exe  D:\user\learn_xuexi\SQL\redis2.conf  --slaveof 127.0.0.1 6379
服务器运行程序        conf文件路径                           主数据库
```



#### 开启客户端

6380代表端口，默认6379

```txt
redis-cli -p 6380
```



#### 开启哨兵

监视数据库，防止没有主数据库

```txt
redis-server.exe  D:\user\learn_xuexi\SQL\sentinel.conf  --sentinel
服务器运行程序        conf文件路径                           标注开启的是哨兵
```









# 数据类型



#### 字符串类型

​	类型：string，用于普通的字符串
​	保存：set key value
​	获取：get key
​	删除：del key



#### 哈希类型

​	类型：hash table，用于存储对象，属性与值
​	保存：hset key field value
​	获取：hget key field | hgetall key
​	删除：hdel key field



#### 列表类型

​	类型：linked list，链表结构，用于【重复】数据集合
​	保存：lpush key value | rpush key value
​	获取：lrange key start end （-1 表示最后）
​	删除：lpop key | rpop key （删除并返回）
​	修剪：ltrim key start end 删除指定【范围之外】的数据



#### 集合类型

​	类型：set，用于【不允许重复】的元素集合
​	保存：sadd key value
​	获取：smembers key
​	删除：srem key value
​	数量：scard key



#### 有序集合类型

​	类型：zset=sorted set，用于【有序的集合】，不允许重复但有顺序
​	保存：zadd key score value
​	获取：zrange key start end [withscores]
​	删除：zrem key value
​	数量：zcard key 获取个数；zcount key min max 获取指定分数下key个数









# 通用命令

	1. keys *：
		* - 查询所有的键
		? - 匹配一个字符
		[] - 匹配指定字符，比如[abc]
		\x - 转义，匹配特殊的x字符
	2. exists key：查看指定键是否存在
	3. type key：获取键对应的value的类型
	4. del key：删除指定的key value（全部删除）
	5. incr/decr key：增加/减少key的值，key的值必须为整数类型！
	6. incrby/decrby key xxx：同上，增加/减少指定的整数值
	7. flushdb/flushall：删除所有数据









# 事务

​	Redis不支持关系型事务中的【回滚】操作
​	命令：
​		multi
​		多条操作语句
​		exec
​	观察：
​		watch key用于观察键是否被修改
​		unwatch 取消观察
​		观察指定key后，如果key没有修改事务将正常进行
​		如果观察的key被修改，那么事务会取消
​		配合事务进行数据操作，保证操作的一致性
​	过期：
​		expire key time
​		指定过期时间后，改建将会被删除
​		Java可以使用setex()方法存储可以指定过期时间的 key value
​		比如：jedis.setex("code",20,"1234"); // 将键值对存入redis并且20秒后自动删除











# 订阅

发布消息：publish 通道 消息
	订阅消息：subscribe 通道
	Jedis：
		新建类继承JedisPubSub作为接收、取消订阅对象
		Jedis.subscribe(new JedisPubSub(), "订阅channel名字");
		JedisPubSub.unsubscribe("订阅channel名字");
		Jedis.publish("channel", "消息");

















# Spring整合

#### spring.xml

```xml
<!--导入外部配置文件-->
    <bean class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">
        <property name="locations">
            <array>
                <value>classpath:jdbc.properties</value>
                <value>classpath:redis.properties</value>
            </array>
        </property>
    </bean>
    <!-- 配置JedisPoolConfig,redis连接池所需要的配置-->
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxTotal" value="${redis.pool.maxTotal}"/>
        <property name="maxIdle" value="${redis.pool.maxIdle}"/>
        <property name="minIdle" value="${redis.pool.minIdle}"/>
    </bean>
    <!--配置redis连接池-->
    <bean id="jedisPool" class="redis.clients.jedis.JedisPool">
        <constructor-arg name="poolConfig" ref="jedisPoolConfig"/>
        <constructor-arg name="host" value="${redis.ip}"/>
        <constructor-arg name="port" value="${redis.port}"/>
    </bean>
```





#### Service使用

```java

//- 服务层相关的bean
@Service
//生产带final属性的构造函数
@RequiredArgsConstructor
public class RedisService {

    private final JedisPool pool;

    //页面访问数
    public String count(User user) {
        try (Jedis jedis = pool.getResource()) {
			jedis.set("user", JSON.toJSONString(user));
            //设置缓存存在时间
            jedis.expire("user" + id,3600L);
			User user = JSON.parseObject(jedis.get("user"), User.class);
        }
    }
```

