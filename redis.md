# redis
1. 概念
    
    * Redis是一款高性能的nosql系列的非关系型数据库
2. 下载安装
3. 命令操作
    1. Redis数据结构
        * key,value,其中key都是字符串，value有五种不同的结构：
        1. 字符串 string
        2. 哈希 hash : map格式
        3. 列表 list : linkedlist
        4. 集合 set : 不允许重复元素
        5. 有序集合 sortedset : 不允许重复元素,且元素有顺序
    2. 字符串类型string
        1. 存储 set key value
        2. 获取 get key
        3. 删除 del key
    3. hash类型
        1. 存储 hset key field value
        2. 获取 hget key field 获取制定field对应的值  /  hgetall  获取all
        3. 删除 hdel key field
    4. 列表list
        1. 添加:
            1. lpush key value 将指定的元素加入列表左边
            2. rpush key value 将元素加入列表右边
        2. 获取:
            * lrange key start end :范围获取
        3. 删除:
            * lpop key:从列表左边删除元素,并将元素返回
            * rpop key:从右边删除
    5. 集合类型set:不允许重复元素
        1. 存储:sadd key value
        2. 获取:smembers key:获取set中所有元素
        3. 删除:srem key value:删除set集合中某个元素
    6. sortedset:不允许重复,元素有序
        1. 存储:zadd key score value: 按照score排序
        2. 获取:zrange key start end
        3. 删除zrem key value
    7. 通用命令:
        1. keys * (*相当于正则表达式)查询所有的键
        2. type key 获取value类型
        3. del key 删除指定的key value
4. 持久化
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

5. java客户端操作Redis
    * jedis：java操纵数据库的工具。
    * 使用步骤：
        1. 下载jedis的jar包
        2. 使用