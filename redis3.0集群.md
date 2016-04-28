##redis3.0集群搭建



1. <a href="#redis 介绍与安装">redis 介绍与安装</a>
2. <a href="#redis 创建集群">redis 创建集群</a>
3. <a href="#redis-cli使用集群">redis-cli使用集群</a>
4. <a href="#java访问redis集群">java访问redis集群</a>
5. <a href="#spring-data访问redis集群">spring-data访问redis集群</a>
6. <a href="#redis集群分片">redis集群分片</a>
7. <a href="#特别说明">特别说明</a>



#####【<a name="redis 介绍与安装" id="redis 介绍与安装" ><font color=black>redis 介绍与安装</font></a>】

  Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。
从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。
  Redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、
set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
  Redis支持主从同步。数据可以从主服务器向任意数量的从服务器上同步，从服务器可以是关联其他从服务器的主服务器。
这使得Redis可执行单层树复制。存盘可以有意无意的对数据进行写操作。由于完全实现了发布/订阅机制，使得从数据库在任何地方同步树时，可订阅一个频道并接收主服务器完整的消息发布记录。同步对读取操作的可扩展性和数据冗余很有帮助。
    
  官网:http://redis.io 有安装说明，这里不在说明。
    
  Redis 3.0的最重要特征是对Redis集群的支持，此外，该版本相对于2.8版本在性能、稳定性等方面都有了重大提高。


#####【<a name="redis 创建集群" id="redis 创建集群"><font color=black>redis 创建集群</font></a>】   

    详细安装步聚请参考:http://blog.csdn.net/xu470438000/article/details/42971091
    官网集群说明参考:http://redis.io/topics/cluster-tutorial

注意：redis2.x的版本不支持集群模式。
   官网说明文档集群需要六个节点。要让集群正常工作至少需要3个主节点，在这里我们要创建6个redis节点，其中三个为主节点，三个为从节点。
   
简易说明：   
   mkdir 7000 7001 7002 7003 7004 7005
   每个目录放一个redis.conf
   port 7000 每个目录名为端口
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 5000
   appendonly yes

   启动 分别进入每一个目录启动
    cd 7000/
    ../../redis-3.0.1/src/redis-server redis.conf
    cd ../7001/
    ../../redis-3.0.1/src/redis-server redis.conf
    ......
    cd ../7005/
    ../../redis-3.0.1/src/redis-server redis.conf

需要一些环境： sudo apt-get install ruby
             sudo apt-get install rubygems
             gem install redis
创建集群：默认前三台为主节点，后三台为从节点. 选项 --replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。
./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
输入yes创建成功

#####【<a name="redis-cli使用集群" id="redis-cli使用集群"><font color=black>redis-cli使用集群</font></a>】

redis-cli -c -p 7000  客户端连接，注意-c参数，查找时不在本端口，会自动切换到有数据的那个端口下。

127.0.0.1:7000> cluster nodes       
     7a6121a5d8c87fc5345f6812e41c83f5163f2db6 127.0.0.1:7002 master - 0 1458546962094 3 connected 10923-16383
    9939a801cf27710cd3ecbcde33b0f1d15b4af834 127.0.0.1:7000 myself,master - 0 0 1 connected 0-5460
    1e60c2a340fc70a812392f098eec97c20557954b 127.0.0.1:7001 master - 0 1458546961593 2 connected 5461-10922
    4af9487baf21d763d7872436677af3404ba5dccd 127.0.0.1:7004 slave 1e60c2a340fc70a812392f098eec97c20557954b 0 1458546961092 5 connected
    011d4c450f6f21d05ed655f6b57020ad284e9cf8 127.0.0.1:7003 slave 9939a801cf27710cd3ecbcde33b0f1d15b4af834 0 1458546960592 4 connected
    e6a251b28ad454dbea3bd972cb7d128403c0415e 127.0.0.1:7005 slave 7a6121a5d8c87fc5345f6812e41c83f5163f2db6 0 1458546961593 6 connected

  用于查看当前Redis节点 所属的Redis集群中的所有节点。
  master表示主，slave表示从，slave后的id对应主的id。myself表示在当前那一台。
  
    127.0.0.1:7000> cluster slots
      1) 1) (integer) 10923
         2) (integer) 16383
         3) 1) "127.0.0.1"
            2) (integer) 7002
         4) 1) "127.0.0.1"
            2) (integer) 7005
      2) 1) (integer) 0
         2) (integer) 5460
         3) 1) "127.0.0.1"
            2) (integer) 7000
         4) 1) "127.0.0.1"
            2) (integer) 7003
      3) 1) (integer) 5461
         2) (integer) 10922
         3) 1) "127.0.0.1"
            2) (integer) 7001
         4) 1) "127.0.0.1"
            2) (integer) 7004

  查看当前的集群状态，以数组形式展示。
  
    127.0.0.1:7000> cluster info
    cluster_state:ok
    cluster_slots_assigned:16384
    cluster_slots_ok:16384
    cluster_slots_pfail:0
    cluster_slots_fail:0
    cluster_known_nodes:6
    cluster_size:3
    cluster_current_epoch:7
    cluster_my_epoch:3
    cluster_stats_messages_sent:26914
    cluster_stats_messages_received:26281
  
   用于查看当前Redis节点所属的Redis集群的整体状态。

    例：
    set beyond "aaa"  会自动保存到一个节点，并切换到对应节点 如：7001
    get abc  会自动去查找，找到返回数据，没有找到返回(nil) 
    从上面知道：7001是主，7004是从，我把7001停止后，在查找abc仍然能找到（这时7004是主了），7004也停止后，找不到。

  
#####【<a name="java访问redis集群" id="java访问redis集群"><font color=black>java访问redis集群</font></a>】

        <dependency>
			<groupId>redis.clients</groupId>
			<artifactId>jedis</artifactId>
			<version>2.7.0</version>
		</dependency>
  
    package com.afmobi;

    import java.util.HashSet;
    import java.util.Set;
    import org.junit.Test;
    import redis.clients.jedis.HostAndPort;
    import redis.clients.jedis.JedisCluster;

    public class TestJedisCluster{
	  private static JedisCluster jc;  
	  static {  
	     //只给集群里一个实例就可以  
	      Set<HostAndPort> jedisClusterNodes = new HashSet<HostAndPort>();  
	      jedisClusterNodes.add(new HostAndPort("127.0.0.1", 7000));  
	      jedisClusterNodes.add(new HostAndPort("127.0.0.1", 7001));  
	      jedisClusterNodes.add(new HostAndPort("127.0.0.1", 7002));
	      jedisClusterNodes.add(new HostAndPort("127.0.0.1", 7003));  
	      jedisClusterNodes.add(new HostAndPort("127.0.0.1", 7004));  
	      jedisClusterNodes.add(new HostAndPort("127.0.0.1", 7005));
	      jc = new JedisCluster(jedisClusterNodes);  
	  }  
	
	@Test
	public void testredis() {
		//jc.set("foo", "bar");
		String value = jc.get("foo");
		System.out.println(value);
		System.out.println(jc.get("beyond"));
	}
	
}


#####【<a name="spring-data访问redis集群" id="spring-data访问redis集群"><font color=black>spring-data访问redis集群</font></a>】

Maven pom.xml配置

<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.7.1.RELEASE</version>
</dependency>

spring xml配置
<bean id="clusterRedisNodes0"   class="org.springframework.data.redis.connection.RedisNode">
        <constructor-arg value="127.0.0.1" />
        <constructor-arg value="7000" type="int" />
    </bean>

    <bean id="clusterRedisNodes1"   class="org.springframework.data.redis.connection.RedisNode">
        <constructor-arg value="127.0.0.1" />
        <constructor-arg value="7001" type="int" />
    </bean>
    ..............
    
    <bean id="redisClusterConfiguration"               class="org.springframework.data.redis.connection.RedisClusterConfiguration">
        <property name="clusterNodes">
            <set>
                <ref bean="clusterRedisNodes0"/>
                ................
                <ref bean="clusterRedisNodes5"/>
            </set>

        </property>
    </bean>
    
    <bean id="jedisConnFactory"   class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory" p:use-pool="true">
        <constructor-arg ref="redisClusterConfiguration" />
    </bean>

    <bean id="redisTemplate"   class="org.springframework.data.redis.core.RedisTemplate"  p:connection-factory-ref="jedisConnFactory" >
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
    </bean>
    
 java 代码中使用
    @Autowired
	private RedisTemplate<String, String> redisTemplate;
    redisTemplate.opsForValue().set("test", "test");


#####【<a name="redis集群分片" id="redis集群分片"><font color=black>redis集群分片</font></a>】

  当集群的redis内存不够用时，需要新增加节点
  新增加一台7006,配置与上面的一样，启动后
  在集群中新增加一台节点，默认为主节点
  ./redis-trib.rb add-node 127.0.0.1:7006 127.0.0.1:7000
  也可以让它成为从节点，7fec61c9df3e7d739916d63a5d1172a23d169c72（需要做为它的从节点ID）
  127.0.0.1:7006>cluster replicate 7fec61c9df3e7d739916d63a5d1172a23d169c72
  
  新增加的节点是无法使用的，因为哈希槽为0，需要分片分配哈希槽(看特别说明)
  ./redis-trib.rb reshard 127.0.0.1:7000
  
#####【<a name="特别说明" id="特别说明"><font color=black>特别说明</font></a>】
 
  说明：Redis集群使用数据分片（sharding）而非一致性哈希（consistency hashing）来实现：一个 Redis集群包含16384个哈希槽（hash slot）， 数据库中的每个键都属于这16384个哈希槽的其中一个，集群使用公式CRC16(key)%16384来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和 。
集群中的每个节点负责处理一部分哈希槽。 举个例子，一个集群可以有三个哈希槽， 其中：
    节点 A 负责处理 0 号至 5500 号哈希槽。
    节点 B 负责处理 5501 号至 11000 号哈希槽。
    节点 C 负责处理 11001 号至 16384 号哈希槽。
这种将哈希槽分布到不同节点的做法使得用户可以很容易地向集群中添加或者删除节点。 比如说：
    如果用户将新节点 D 添加到集群中， 那么集群只需要将节点 A 、B 、 C 中的某些槽移动到节点 D 就可以了。
    与此类似， 如果用户要从集群中移除节点 A ， 那么集群只需要将节点 A 中的所有哈希槽移动到节点 B 和节点 C ， 然后再移除空白（不包含任何哈希槽）的节点 A 就可以了。
因为将一个哈希槽从一个节点移动到另一个节点不会造成节点阻塞， 所以无论是添加新节点还是移除已存在节点， 又或者改变某个节点包含的哈希槽数量， 都不会造成集群下线。

注意：比如A B C是主节点，它们每台都有一个从节点。
     当B出问题了，它的从节点会自动成为主节点，如果B主节点从节点都出问题了，B 负责处理 5501 号至 11000 号哈希槽就处理不了，整个集群就出问题了。
     想移除B节点之前，先备份它的哈希槽到其他节点之后在移除。
  
  
  
  
