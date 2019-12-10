# Redis学习笔记
## 介绍
NoSQL、 Key-Value键值存储数据库</br>
NoSQL数据库适用场景：</br>
1. 数据模型比较简单
2. 需要灵活性更强的IT系统
3. 对数据库性能要求较高
4. 不需要高度的数据一致性
5. 对于给定Key,比较容易映射复杂值的环境</br>
### Redis是一个简单的、高效的、分布式的、基于内存的缓存工具。
* 支持多种数据结构（String, List, Hash, Set, ZSet）
* 单线程，原子性
* 可以持久化，因为使用了RDB和AOF机制
* 支持集群，而且支持16个(0-15)库
* 可以用作消息队列
-----------------
***企业级开发中可以用作数据库、缓存、消息中间件***
</br>
## Redis使用
1. 打开一个 cmd 窗口 使用cd命令切换到Redis目录并运行 
```shell
redis-server.exe  redis.windows.conf 
```
* 也可以将Redis服务端注册为Windows服务
```shell
redis-server --service-install redis.windows-service.conf --loglevel verbose
```
* 启动命令改为：
```shell
redis-server --service-start
```

2. 这时候另启一个cmd窗口，原来的不要关闭，不然就无法访问服务端了。切换到redis目录下运行</br>
```shell
redis-cli.exe -h 127.0.0.1 -p 6379
```
-------------------------------
***接下来就开始愉快的学习Redis啦！***
## 综合语法
```shell
# 查看该key是什么类型
type key

# 删除一个key的所有值,则这个key也被删除，redis不存在没有值的key
hdel key field field1 ...

# 能够删除所有类型的key
del key key1 ...

# 数据库的切换
select 数据库
 
# 移动数据
move key 数据库

# 清除当前数据库所有key
flushdb

# 清除整个redis的数据库所有key
flushall

```

## Redis之String类型：
```shell
# 设置过期时间
# 应用场景：限时优惠券活动等
expire key seconds

# 在key不存在时设置key的值
# 解决分布式锁的方案之一
setnx key value

# 用于存取指定key中 指定范围的值
getrange key start end

# 用于设置、获取多个key
mset key1 value1 key2 value2

# 用于设置指定key的值，并返回key的旧值
getset key value

# 返回key所存储的值的长度
strlen key

# 使value自增1,key不存在自动创建并+1
incr key
decr key

# 设置自增值/自减值
incrby key increment
decrby key decrement
```
### 应用场景：
1. String通常用于保存单个字符串或者JSON字符串
2. 因String是二进制安全的，可以用来存储一个图片内容
3. 计数器(常规key-value缓存应用，常规计数：微博数，粉丝数)。
incr等指令有***原子操作***的特性
## Redis之Hash  : 类似一个JavaBean
适用于存储对象，Key为String类型。
### 一些语法：
```shell
# 设置key 的字段及值
hset key field value

# 得到key 的字段的值
hget key field

# 同时设置/获取多个字段
#redis中每个hash可以存2^32-1个键值对
hmset key field value field1 value1 ...

# 获取key的所有字段及值
hgetall key

# 获取key中所有字段名
hkeys key

# 获取key中字段数量
hlen key

# 删除key中的字段
hdel key field field1 ...

# 当field不存在时，才会赋值
hsetnx key field value

# 给指定field的值加上指定数   此时不可以加小数
hincrby key field increment

# 浮点型    可加小数和整数
hincrbyfloat key field increment

# 查看是否存在某个field
hexists key field

```
***虽然String类型也可以存储对象，如下两种方式：***</br>
key|value|
--|:--:|--:
id:1|name:wang,age:12|
user:id |1
user:name|wang
***但是第一种会有序列化/反序列化开销，并且修改时需要修改全部。
第二种会重复存储。 可见，由于性能问题，产生了*** **hash**
## Redis之List类型
相当于Java中的LinkedList类型
```shell
# 将一个或多个值插入到列表左侧
lpush key value value2 ...

# 将一个或多个值插入到列表右侧 
rpush key value value2 ...

# 将值插入到已存在的列表左侧,如果列表不存在，操作无效
lpushx key value
# 。。。。。。。。。。。右侧，。。。。。。。。。。。。
rpushx key value

# 获取从start到stop位置的value
lrange key start stop
# 第一个元素位置是0,第二个是1,....
## 最后一个元素位置是-1,倒数第二个是-2,....
lrange key 0 -1

## 获取列表长度
llen key

## 通过索引获取列表中的元素
lindex key index

## 移除并返回列表的第一个元素（从左侧删除）
lpop key

## 移除并返回列表最后一个元素（从右侧删除）
rpop key

## 移除并返回列表的第一个/最后一个元素，如果列表没有元素或者没有该列表，则会阻塞列表直到等待超时或发现可弹出元素为止
blpop key timeout
brpop key timeout

## 对列表进行修剪，只保留指定区域内的元素
ltrim key start stop

## 通过索引修改指定位置的元素的值
lset key index value

## 在列表的某个元素(pivot)的前或者后插入元素
linsert key before|after pivot value

# 将source右边的元素移到destination的左侧
rpoplpush source destination

# 循环列表，将l1最右侧移动到最左侧
rpoplpush l1 l1
```
### 应用场景:
***项目常应用于：***</br></br>
1. 对数据量大的集合数据删减</br>
列表数据显示、关注列表、粉丝列表、留言评价等，分页（lrange），热点新闻等。

2. 任务队列通常用来实现一个消息队列，而且可以保证先后顺序，不必像mysql样需要通过order by来排序。</br>
* 生产者消费者模式</br>
常用案例：订单系统的下单流程，用户系统登录注册短信等。
## Redis之Set类型
相当于java中的Hashtable集合</br>
Redis中的Set是String类型的无序且元素唯一集合。
```shell
## 赋值语法：
## 向集合添加一个或多个成员
sadd key member1 [member2]
## 取值语法:
## 获取集合的成员数量
scard key 
## 返回集合中的所有成员
smembers key
## 判断member元素是否存在于集合
sismember key
## 返回集合中一个或多个随机数,可用于抽奖功能
srandmember key [count]
## 删除语法：
## 移除集合中一个或多个成员
srem key member1 [member2]
## 移除并返回集合中一个或多个随机数,可用于抽奖功能
spop key [count]
## 将member元素从source集合移动到destination集合
smove source destination member
## 差集语法：
## 返回给定所有集合的差集(左侧集合为主)
sdiff key1 [key2]
## 返回给定所有集合的差集并存储在destination集合中(会覆盖掉)
sdiffstore destination key1 [key2]
## 交集语法：
## 返回给定所有集合的交集
sinter key1 [key2]
## 返回给定所有集合的交集并存储在destination集合中(会覆盖掉)
sinterstore destination key1 [key2]
## 并集语法：
## 返回所有给定集合的并集
sunion key1 [key2]
## 返回所有给定集合的并集并存储在destination中
sunionstore destination key1 [key2]
```
### 应用场景:
***项目常应用于：对两个集合间的数据【计算】，进行交集、差集、并集运算***</br></br>
1. 以非常方便的实现如共同关注、共同喜好、二度好友等功能。对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端或者存储到新的集合中。
2. 利用唯一性，可以统计访问网站的所有独立IP。
## Redis之sorted set(有序集合)类型，ZSET
***有序且不重复的集合***
1. Redis集合和集合一样也是string类型元素的集合，且不允许重复的成员。
2. 不同的是每个元素都会关联一个double类型的分数。Redis正是通过分数来为集合中的成员进行从小到大的排序。
3. 有序集合的成员是唯一的，但分数(score)却可以重复。
4. 集合是通过哈希表实现的，所以添加、删除、查找的复杂度都是o(1)。</br>
```shell
## 赋值语法：
## 向有序集合添加一个或多个成员，或者更新已存在成员的分数
zadd key score1 member1 [score2 member2]
## 取值语法:
## 获取有序集合的成员数
zcard key
## 计算在有序集合中指定区间分数的成员数
acount key min max
## 返回有序集合中指定成员的索引
zrank key member
## 通过索引区间返回有序集合的指定区间内的成员(低到高)
zrange key start stop [WITHSCORES]
##返回有序集合中指定区间内的成员，通过索引，分数从高到低
zrevrange key start stop [WITHSCORES]
## 删除语法：
## 移除有序集合中的一个或多个成员
zrem key member [member...]
## 移除有序集合中给定的排名区间的所有成员(默认低到高)
zremrangebyrank key start stop
## 移除有序集合中给定分数区间的所有成员
zremrangebyscore key min max
```
### 应用场景:
***项目常应用于：排行榜***</br></br>

1. 比如twitter的public timeline可以以发表时间作为score来存储，这样获取时自动按时间排好序。
2. 比如一个存储全班同学成绩的Sorted Set,其集合value可以是学号，score可以是考试分数。这样在数据插入到集合的时候，就已经进行了天然的排序。
3. 还可以用来做带权重的队列，让重要的任务优先执行。

## Redis之发布订阅
***Redis发布订阅(pub/sub)是一种消息通信模式***
```shell
## 订阅频道:
## 订阅给定的一个或多个频道的信息
subscribe channel [channel]
## 订阅一个或多个符合给定模式的频道
psubscribe pattern [pattern]
## 发布频道：
## 将信息发送到指定的频道
publish channel message
## 退订频道：
unsubscribe channel [channel...]
## 退订所有给定模式的频道
punsubscribe pattern [pattern]
```
### 应用场景:
***项目常应用于：构建实时消息系统，比如普通的即时聊天，群聊等功能***</br></br>
1. 在一个博客网站中，有100个粉丝订阅了你，当发布新文章就可以推送消息给粉丝们。
2. 微信公众号模式。
## Redis之事务：
1. Redis会将一个事务中的所有命令序列化，然后按顺序执行。
2. 执行中不会被其他命令插入，不允许出现加塞行为。
</br>
</br>
***一个事务从开始到执行会经历一下三个阶段***
* 开始事务
* 命令入队
* 执行事务
</br>
</br>
***事务的错误处理***
* 队列中某个命令出现了报告错误【**是指输入命令时，输错了一行**】，执行时整个的所有队列都会被取消，即事务的回滚。
* 若是语法问题【**比如对string类型执行了自增操作**】,则只有出错的命令不会被执行，其他会正常执行。
```shell

## 输入此命令后，接下来输入的命令依次进入命令队列中，不会立即执行。(注意：若某个命令出现了错误，所有命令都执行失败)
multi

## 输入此命令后，Redis将之前队列中的命令依次执行
exec

## 放弃队列执行
discard

## 监视一个或多个key,如果在事务执行之前这个(或这些)key被其他命令所改动，那么事务被打断(注意：在此事务之前)
watch key [key...]

## 取消watch命令对所有key的监视
unwatch
```

### 应用场景:
***项目常应用于***</br></br>
* 一组命令必须同时都执行，或者都不执行
* 保证一组命令在执行的过程中不被其他命令插入
1. 商品秒杀活动
2. 转账活动
## Redis之脚本
***Redis脚本使用Lua解释器来执行脚本。***
```shell
## 执行脚本常用命令为
eval
```
## Redis之数据淘汰策略
* ***当内存不足时，Redis会根据配置的缓存策略淘汰部分keys,以保证写入成功。当无淘汰策略时或者没有找到适合淘汰的key时，Redis直接返回out of memory错误。***
* ***允许用户设置最大使用内存，maxmemory:512G***
* ***建议：了解Redis的淘汰策略之后，在平时使用时尽量主动设置/更新key的expire时间，主动剔除不活跃的旧数据，有助于提升查询性能。***
## Redis之持久化
***数据存放于：***
1. 内存：高效、断电(关机)内存数据会丢失
2. 硬盘：读写速度慢于内存，断电数据不会丢失
***Redis持久化的两种策略：***
1. RDB (占内存)
2. AOF (占硬盘)
## Redis之与数据库的一致性
***解决方案：***
1. 实时同步
2. 异步队列
3. 使用阿里的同步工具canal
4. 使用UDF自定义函数的方式
5. 使用Lua脚本
6. 做定时任务
## Redis之重要概念
1. ***穿透***</br>
*概念：*</br>
查询一个一定不存在的数据，则每次请求都会访问数据库，造成缓存穿透。
*解决办法：*
持久层查询不到就缓存空结果，需要设置空缓存的过期时间。
2. ***雪崩***</br>
*概念：*</br>
缓存大量失效的时候，引发大量查询数据库。
*解决办法：*
 * 用锁/分布式锁或者队列串行访问
 * 缓存失效时间均匀分布
3. ***热点key***</br>
*概念：*</br>
某个key访问非常频繁，当key失效时有大量线程构建缓存，导致负载增加，系统崩溃。
*解决办法：*
* 使用锁，单机使用synchronized/lock等，分布式使用分布式锁。
* 缓存过期时间不设置，而是设置在key对应的value里。如果检测到存的时间超过过期时间则异步更新缓存。
* 在value设置一个比过期时间t0小的过期时间t1,当t1过期的时候，延长t1并做更新缓存操作。
* 设置标签缓存，标签缓存设置过期时间，标签缓存过期后，需异步更新实际缓存。
## Redis高级
***项目中只使用一台Redis面临的问题***
</br>
1. 结构上:单台Redis服务器会发生单点故障，而且一台Redis处理所有请求负载，压力较大。
2. 容量上:单个Redis服务器内存容量有限，即使最大容量为256G,但最大使用内存不应该超过20G。
</br>
***系统应该具有：***
1. 容错性
2. 高并发
3. 高可用
</br>
***提升系统的并发能力:***
1. 垂直扩展</br>
    * 硬件
    * 架构
2. 水平扩展</br>
    就是增加服务器数量

## Redis之主从复制
***现象：多读少写***
</br>
* master只负责写，slave只负责读
* 读写分离，提高服务器的负载能力，还可以根据请求规模自由增加/减少从库的数量
* 数据被复制成多份，若一台服务器出现故障，也可以使用其他服务器快速恢复

## Redis之集群
***Redis集群搭建方案：***
1. Twitter开发的twemproxy
2. 豌豆荚开发的codis
3. redis官方的redis-cluster

Redis搭建集群的方式有多种，但Redis 3.0之后版本支持redis cluster,至少需要3(master) + 3(slave)才能建立集群。Redis cluster采用无中心化结构，每个节点保存数据和整个集群状态，每个节点都和其他所有节点连接。

***搭建***
* 集群中应该有奇数个节点(master)，所以搭建集群最少有3台服务器，同时每个master至少有一个备份节点，因而至少创建6台服务器才能搭建一个cluster集群。

## Java连接Reids : Jedis
//依赖Jar
```xml
<dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
```
***关于host地址，查看reids目录下的conf文件发现：***
```shell
bind 127.0.0.1
```
### 简单测试是否连接成功
```java
public class RedisDemo {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 6379;
        Jedis jedis = new Jedis(host, port);
        System.out.println(jedis.ping());
        //关闭
        jedis.close();
    }
}
```
### 实际开发应用场景之String
```java
public class RedisDemo {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 6379;
        Jedis jedis = new Jedis(host, port);
        /**
         * Redis作用：为了减轻数据库的访问压力
         * 需求：判断某key是否存在，如果存在，就从Redis中查询
         * 如果不存在，查询数据库，且将查询出的数据存入Redis
         */
        String key = "applicationName"; //key
        if (jedis.exists(key)) {
            String fromRedis = jedis.get(key);
            System.out.println("返回前端001:"+fromRedis);
        } else {
            //从数据库中查出结果后
            String fromMySQL = "支付宝";
            String result = jedis.set(key, fromMySQL);
            if ("OK".equals(result)) {
                System.out.println("返回前端002："+fromMySQL);
            }
        }
        jedis.close();

    }
}
```
### Jedis连接池使用
```java
public class RedisDemo {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 6379;
        //连接池 Redis POOL 基本配置信息
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(5);//最大连接数
        poolConfig.setMaxIdle(1);//最大空闲数
        //.......
        //连接池
        JedisPool pool = new JedisPool(poolConfig, host, port);
        //获取Jedis连接  
        Jedis jedis = pool.getResource();
        System.out.println(jedis.ping());
        jedis.close();

    }
}
```
***将获取Jedis连接和关闭Jedis封装起来***</br>
//类RedisUtil
```java
public class RedisUtil {
    private static JedisPool pool;
    static {
        String host = "127.0.0.1";
        int port = 6379;
        //连接池 Redis POOL 基本配置信息
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(5);//最大连接数
        poolConfig.setMaxIdle(1);//最大空闲数
        //.......
        //连接池
        pool = new JedisPool(poolConfig, host, port);
    }
    public static Jedis getJedis() {
        return pool.getResource();
    }
    public static void closeJedis(Jedis jedis) {
        jedis.close();
    }
}
```
//测试类
```java
public class RedisDemo {
    public static void main(String[] args) {
        //获取Jedis连接
        Jedis jedis = RedisUtil.getJedis();
        System.out.println(jedis.ping());
        RedisUtil.closeJedis(jedis); }
}
```
### 实际开发应用场景之Hash
```java
public class RedisDemo {
    public static void main(String[] args) {
        //获取Jedis连接
        Jedis jedis = RedisUtil.getJedis();
        /**
         * Redis中的Hash类型使用：
         */
        String key = "user";
        if (jedis.exists(key)) {
            //存在就在redis中获取
            Map<String,String> resultMap = jedis.hgetAll(key);
            System.out.println("返前端001"+resultMap.get("id"));
        } {
            //不存在先去数据库查，在存到redis
            //模拟从数据库中查到数据
            User user = new User();
            user.setId(100);
            user.setName("汪汪王");

            //存入redis
            Map<String,String> map = new HashMap<>();
            map.put("id", user.getId().toString());
            map.put("name", user.getName());
            jedis.hmset(key, map);
            System.out.println("返前端002");
        }

        RedisUtil.closeJedis(jedis); }
}
```
## RedisTemplate : Redis封装工具类
//依赖jar
```java
 <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
            <version>1.4.2.RELEASE</version>
        </dependency>
```






  


