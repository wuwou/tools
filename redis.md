- Redis安装方法：
  
  ```cpp
  linux下：
  # wget http://download.redis.io/releases/redis-6.0.8.tar.gz
  # tar xzf redis-6.0.8.tar.gz
  # cd redis-6.0.8
  # make
  # cd src
  # ./redis-server     
  ```

这时候就启动了redis服务

![](https://github.com/wuwou/tools/blob/master/image/1123_1.PNG)

再打开另一个窗口，原来的不要关闭，不然就无法访问服务端了。

```cpp
# cd src
# ./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```

启动redis后服务进程后，就可以使用测试客户端服务程序redis-cli和redis服务交互了。

![](https://github.com/wuwou/tools/blob/master/image/1123_2.PNG)

- Redis中5中基础数据结构

**string**：字符串结构用的最多的是存储用户信息，将用户信息结构用JSON序列化成字符串，然后将序列化后的字符串塞进Redis来缓存。取信息会经过一次反序列化的过程。

Redis的字符串是动态字符串，采用预分配冗余空间的方式来减少内存的频繁分配。

string中的常见指令有：set, get, mset, mget, expire, setex, getex, incr, incrby, ttl来查看剩余时间

**list**：Redis的list相当于双向链表，插入删除快，索引慢。

list中的常见指令有：llen, lpop, lpush, rpop, rpush, lindex, ltrim, lrange(注意没有rindex, rrange, rtrim)

list探究低层原理，用的是特殊的存储结构，即将一段段连续的内存空间用链表连接起来。

**hash**：即key->value的映射，其中value会发生碰撞。

Redis的hash字典值只能是字符串，并且rehash方式不一样，采用了渐进式rehash。(在字典很大的时候，将旧hash的内容一点点迁移到新的hash结构中)

hash中常见指令有：hset, hgetall, hlen, hget, hset, hmset, hincrby

**set**：set只有key，内部保存无序，唯一的key值

set中常见指令有：sadd, smembers, sismember, scard, spop

**zset (important)**：有唯一的value，每个value有一个score，按score进行排序(默认是增序)，且score使用double类型进行存储。

zset中常见指令有：zadd, zrange, zrevrange, zcard, zscore, zrank, zrangebyscore, zrem, zrangebyscore...withscore

zset内部使用跳跃列表实现。

---

- Redis分布式锁

怎样实现两种操作的






























