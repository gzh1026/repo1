# redis

remote dictionary server：远程字典服务器

**特点：**consistency（强一致性）available（可用性）partition tolerance（分区容错性）

**三个特点只能满足两个**，分区容错性必须要满足，只能在一致性和可用性之间权衡。

**base**：基本可用（basically available），软状态（soft state），最终一致性（eventually consistent）：他的思想是通过让系统放松对某一时刻数据一致性的要求来换取**系统整体伸缩性和性能上的改观**。

redis索引都是从0开始，默认端口都是6379

**基本语法**：

```
查看所有数据库
keys *
清空当前库的所有数据库跑路
FLUSHDB
清空所有库的数据库跑路
FLUSHALL
选择数据库
select I（int） 
查看当前数据库key数量
bdsize
判断key是否存在
exists key
为给定的key设置过期时间
expire key
查看还有多少秒过期，-1表示永不过期，-2表示已过期
ttl key
查看key的类型
type key
```

Redis数据结构

key,value,其中key都是字符串，value有五种不同的结构：

字符串 **string**二进制字符安全，redis的string可以 包含任何数据，比如jpg，或者序列化的对象。一个value最多可以是512m

哈希 hash : map格式

列表 list : linkedlist

集合 set : 不允许重复元素

有序集合 sortedset : 不允许重复元素,且元素有顺序

1. 字符串类型string
    1. 存储 set key value
    2. 获取 get key
    3. 删除 del key

    ```
    incr/decr/incrby/decby 一定要是数字才能加减
    getrange/setrange
    setex（set with expire）键秒值/setnx（set if not exist）
    mset/mget/msetnx
    ```

    

2. hash类型：kv模式不变，但是v是一个map形式
    1. 存储 hset key field value
    2. 获取 hget key field 获取制定field对应的值  /  hgetall  获取all
    3. 删除 hdel key field

    ```
    hset/hget/hmset/hmget/hgetall/hdel
    hlen
    hexists key
    hkeys/hvals
    hincrby/incrbyfloat
    hsetnx
    ```

    

3. 列表list，**底层是链表**
    1. 添加:
        1. lpush key value 将指定的元素加入列表左边
        2. rpush key value 将元素加入列表右边
        
    2. 获取:
       
        * lrange key start end :范围获取
        
    3. 删除:
        * lpop key:从列表左边删除元素,并将元素返回
        * rpop key:从右边删除
        
    
```
    lpush队列/rpush栈/lrange
    lpop/rpop
    lindex 按照索引获得元素，从上到下
    llen
    lrem key 删除n个value
    ltrim key 开始index结束index，截取指定范围的值后再赋值给key
    eg：ltrim list1 3 5
    rpoplpush 源列表目的列表
    lset key index value
    linsert key before/after 值1  值2
    ```

    

4. 集合类型set:不允许重复元素，通过**hashtable**实现
    1. 存储:sadd key value
    2. 获取:smembers key:获取set中所有元素
    3. 删除:srem key value:删除set集合中某个元素

    ```
    add/smenbers/sismember
    scard获取集合里元素个数
    srem key value删除集合中元素
    srandmember key 某个整数（随机出几个数）
    spop key随机出栈
    
    sdiff 差集
    sinter 交集
    sunion 并集
    ```

    

5. sortedset:不允许重复,元素有序。不同的是，每个元素前都会关联一个double类型的分数。通过分数来为集合中成员进行排序，成员唯一，分数可以重复。
    1. 存储:zadd key score value: 按照score排序
    2. 获取:zrange key start end
    3. 删除zrem key value

    ```
    在set基础上，加一个score值，之前set是k1 v1 v1 v2现在是k1 score 1 vi score2 v2
    add/zrange[withscore]  / (  不包含  /limit是返回限制  limit开始下标走，走多少步
    zrem key 某score下对应的value值，作用是删除元素
    zcard/zcount key score区间/zrank key values值  作用是获得下标值/zscore key 对应值，获得分数
    zrevrange
    ```

    

6. 通用命令:
    1. keys * (*相当于正则表达式)查询所有的键
    2. type key 获取value类型
    3. del key 删除指定的key value

7. 持久化
    1. Redis是一个内存是数据库,当Redis服务器重启,获取电脑重启数据会丢失,我们可以将Redis内存中的数据持久化到硬盘文件里.
    2. Redis持久化机制:
        1. RDB:默认配置,不用配置,默认使用此机制
            * 在一定的时间间隔中,检测key的变化情况,然后持久化数据
            1. 编辑redis.windows.conf文件
                * save 900 1
                * save 300 10
                * save 60 10000 
                * after 900 sec (15 min) if at least 1 key changed
                * after 300 sec (5 min) if at least 10 keys changed
                * after 60 sec if at least 10000 keys changed
            2. 在当前目录cmd重启启动服务器文件(D:\javaweb\Redis-x64-3.2.100\redis-server.exe rediswindows.conf)
        2. AOF:日志记录方式,可以记录每一条命令的操作,可以每一次命令操作后可以持久化数据.对性能影响大.
            1. 编辑conf文件,找到appendonly no,将no改为yes
            * no: don't fsync, just let the OS flush the data when it wants. Faster.
            * appendfsync always：每一个操作都持久化
            * always: fsync after every write to the append only log. Slow, Safest.
            * appendfsync ererysec：每隔一秒都进行一次持久化

8. java客户端操作Redis
    * jedis：java操纵数据库的工具。
    * 使用步骤：
        1. 下载jedis的jar包
        2. 使用