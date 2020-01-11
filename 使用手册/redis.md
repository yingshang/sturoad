# redis使用
## redis-server
可以直接使用./redis-server默认启动
```
Usage: ./redis-server [/path/to/redis.conf] [options]
       ./redis-server - (read config from stdin)
       ./redis-server -v or --version
       ./redis-server -h or --help
       ./redis-server --test-memory <megabytes>

Examples:
       ./redis-server (run the server with default conf)
       ./redis-server /etc/redis/6379.conf
       ./redis-server --port 7777
       ./redis-server --port 7777 --slaveof 127.0.0.1 8888
       ./redis-server /etc/myredis.conf --loglevel verbose

Sentinel mode:
       ./redis-server /etc/sentinel.conf --sentinel

```

## redis-cli

```
root@l-virtual-machine:~# redis-cli -h
redis-cli 4.0.9

Usage: redis-cli [OPTIONS] [cmd [arg [arg ...]]]
  -h <hostname>      服务器的IP (default: 127.0.0.1).
  -p <port>          服务器端口 (default: 6379).
  -s <socket>        服务器socket连接 (overrides hostname and port).
  -a <password>      redis密码.
  -u <uri>           Server URI.
  -r <repeat>        Execute specified command N times.
  -i <interval>      When -r is used, waits <interval> seconds per command.
                     It is possible to specify sub-second times like -i 0.1.
  -n <db>            Database number.
  -x                 Read last argument from STDIN.
  -d <delimiter>     Multi-bulk delimiter in for raw formatting (default: \n).
  -c                 Enable cluster mode (follow -ASK and -MOVED redirections).
  --raw              Use raw formatting for replies (default when STDOUT is
                     not a tty).
  --no-raw           Force formatted output even when STDOUT is not a tty.
  --csv              Output in CSV format.
  --stat             Print rolling stats about server: mem, clients, ...
  --latency          Enter a special mode continuously sampling latency.
                     If you use this mode in an interactive session it runs
                     forever displaying real-time stats. Otherwise if --raw or
                     --csv is specified, or if you redirect the output to a non
                     TTY, it samples the latency for 1 second (you can use
                     -i to change the interval), then produces a single output
                     and exits.
  --latency-history  Like --latency but tracking latency changes over time.
                     Default time interval is 15 sec. Change it using -i.
  --latency-dist     Shows latency as a spectrum, requires xterm 256 colors.
                     Default time interval is 1 sec. Change it using -i.
  --lru-test <keys>  Simulate a cache workload with an 80-20 distribution.
  --slave            Simulate a slave showing commands received from the master.
  --rdb <filename>   Transfer an RDB dump from remote server to local file.
  --pipe             Transfer raw Redis protocol from stdin to server.
  --pipe-timeout <n> In --pipe mode, abort with error if after sending all data.
                     no reply is received within <n> seconds.
                     Default timeout: 30. Use 0 to wait forever.
  --bigkeys          Sample Redis keys looking for big keys.
  --hotkeys          Sample Redis keys looking for hot keys.
                     only works when maxmemory-policy is *lfu.
  --scan             List all keys using the SCAN command.
  --pattern <pat>    Useful with --scan to specify a SCAN pattern.
  --intrinsic-latency <sec> Run a test to measure intrinsic system latency.
                     The test will run for the specified amount of seconds.
  --eval <file>      Send an EVAL command using the Lua script at <file>.
  --ldb              Used with --eval enable the Redis Lua debugger.
  --ldb-sync-mode    Like --ldb but uses the synchronous Lua debugger, in
                     this mode the server is blocked and script changes are
                     are not rolled back from the server memory.
  --help             Output this help and exit.
  --version          Output version and exit.

Examples:
  cat /etc/passwd | redis-cli -x set mypasswd
  redis-cli get mypasswd
  redis-cli -r 100 lpush mylist x
  redis-cli -r 100 -i 1 info | grep used_memory_human:
  redis-cli --eval myscript.lua key1 key2 , arg1 arg2 arg3
  redis-cli --scan --pattern '*:12345*'

  (Note: when using --eval the comma separates KEYS[] from ARGV[] items)

When no command is given, redis-cli starts in interactive mode.
Type "help" in interactive mode for information on available commands
and settings.
```

## 使用
简单的设置一个值和取值
```
root@l-virtual-machine:~# redis-cli
127.0.0.1:6379> set username test
OK
127.0.0.1:6379> get username
"test"
127.0.0.1:6379> 
```
测试redis是否启动成功
```
127.0.0.1:6379> PING
PONG
```

## redis.conf

```
redis.conf 配置项说明如下：
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    daemonize no
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid
3. 指定Redis监听端口，默认端口为6379，为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    port 6379
4. 绑定的主机地址
    bind 127.0.0.1
5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    timeout 300
6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
    loglevel verbose
7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
    logfile stdout
8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    databases 16
9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    save <seconds> <changes>
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
 
10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    rdbcompression yes
11. 指定本地数据库文件名，默认值为dump.rdb
    dbfilename dump.rdb
12. 指定本地数据库存放目录
    dir ./
13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    slaveof <masterip> <masterport>
14. 当master服务设置了密码保护时，slav服务连接master的密码
    masterauth <master-password>
15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    requirepass foobared
16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxclients 128
17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    maxmemory <bytes>
18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly no
19. 指定更新日志文件名，默认为appendonly.aof
     appendfilename appendonly.aof
20. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折中，默认值）
    appendfsync everysec
 
21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
     vm-enabled no
22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
     vm-swap-file /tmp/redis.swap
23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
     vm-max-memory 0
24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
     vm-page-size 32
25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
     vm-pages 134217728
26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
     vm-max-threads 4
27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    glueoutputbuf yes
28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    hash-max-zipmap-entries 64
    hash-max-zipmap-value 512
29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
    activerehashing yes
30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    include /path/to/local.conf
```

## Redis中的内存维护策略
```
redis作为优秀的中间缓存件，时常会存储大量的数据，即使采取了集群部署来动态扩容，也应该即使的整理内存，维持系统性能。

在redis中有两种解决方案，
一是为数据设置超时时间，
二是采用LRU算法动态将不用的数据删除。内存管理的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据。
1.volatile-lru：设定超时时间的数据中,删除最不常使用的数据.
2.allkeys-lru：查询所有的key中最近最不常使用的数据进行删除，这是应用最广泛的策略.
3.volatile-random：在已经设定了超时的数据中随机删除.
4.allkeys-random：查询所有的key,之后随机删除.
5.volatile-ttl：查询全部设定超时时间的数据,之后排序,将马上将要过期的数据进行删除操作.
6.noeviction：如果设置为该属性,则不会进行删除操作,如果内存溢出则报错返回.
7.volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键
8.allkeys-lfu：从所有键中驱逐使用频率最少的键
```


## redis常见命令
```
keys * ：返回满足的所有键，可以模糊匹配，比如keys abc* 代表abc开头的key
exists key ： 是否存在指定的key，存在返回1，不存在返回0
expire key second ：设置某个key的过期时间 时间为秒
del key ：删除某个key
ttl key ： 查看剩余时间，当key不存在时，返回-2；存在但没有设置生育生存时间时候，返回-1；否则，以秒为单位，返回key的剩余生存时间，
persist key :取消过去时间
pexpire key milliseconds : 修改key的过期时间为毫秒
select ： 选择数据库 数据库为0-15（默认一共16个数据库）
move key dbindex ：将当前数据中的key转移到其他数据库
randomkey ： 随机返回一个key
rename key key2 ：重命名key
echo : 打印命令
dbsize : 查看数据库的key数量
info ： 查看数据库的信息
config get * ： 实时传储收到的请求，返回相关的配置
flushdb ：清空当前数据库
flushall : 清空所有数据库
```

## redis 数据类型

### string

```
setkey_name value：命令不区分大小写，但是key_name区分大小写
SETNX key value：当key不存在时设置key的值。（SET if Not eXists）
get key_name
GETRANGE key start end：获取key中字符串的子字符串，从start开始，end结束
MGET key1 [key2 …]：获取多个key
GETSET KEY_NAME VALUE：设定key的值，并返回key的旧值。当key不存在，返回nil
STRLEN key：返回key所存储的字符串的长度
INCR KEY_NAME ：INCR命令key中存储的值+1,如果不存在key，则key中的值话先被初始化为0再加1
INCRBY KEY_NAME 增量
DECR KEY_NAME：key中的值自减一
DECRBY KEY_NAME
append key_name value：字符串拼接，追加至末尾，如果不存在，为其赋值
```
**应用场景**
```
1、String通常用于保存单个字符串或JSON字符串数据
2、因为String是二进制安全的，所以可以把保密要求高的图片文件内容作为字符串来存储
3、计数器：常规Key-Value缓存应用，如微博数、粉丝数。INCR本身就具有原子性特性，所以不会有线程安全问题

```

### hash
Redis hash是一个string类型的field和value的映射表，hash特别适用于存储对象。每个hash可以存储232-1键值对。可以看成KEY和VALUE的MAP容器。相比于JSON，hash占用很少的内存空间。

**常用命令**
```
HSET key_name field value：为指定的key设定field和value
hmset key field value[field1,value1]
hget key field
hmget key field[field1]
hgetall key：返回hash表中所有字段和值
hkeys key：获取hash表所有字段
hlen key：获取hash表中的字段数量
-hdel key field [field1]：删除一个或多个hash表的字段
```
**应用场景**
```
Hash的应用场景，通常用来存储一个用户信息的对象数据。
1、相比于存储对象的string类型的json串，json串修改单个属性需要将整个值取出来。而hash不需要。
2、相比于多个key-value存储对象，hash节省了很多内存空间
3、如果hash的属性值被删除完，那么hash的key也会被redis删除
```
### list
```
lpush key value1 [value2]
rpush key value1 [value2]
lpushx key value：从左侧插入值，如果list不存在，则不操作
rpushx key value：从右侧插入值，如果list不存在，则不操作
llen key：获取列表长度
lindex key index：获取指定索引的元素
lrange key start stop：获取列表指定范围的元素
lpop key ：从左侧移除第一个元素
prop key：移除列表最后一个元素
blpop key [key1] timeout：移除并获取列表第一个元素，如果列表没有元素会阻塞列表到等待超时或发现可弹出元素为止
brpop key [key1] timeout：移除并获取列表最后一个元素，如果列表没有元素会阻塞列表到等待超时或发现可弹出元素为止
ltrim key start stop ：对列表进行修改，让列表只保留指定区间的元素，不在指定区间的元素就会被删除
lset key index value ：指定索引的值
linsert key before|after world value：在列表元素前或则后插入元素

```
**应用场景**
```
1、对数据大的集合数据删减
		列表显示、关注列表、粉丝列表、留言评价...分页、热点新闻等
2、任务队列
		list通常用来实现一个消息队列，而且可以确保先后顺序，不必像MySQL那样通过order by来排序

rpoplpush list1 list2 移除list1最后一个元素，并将该元素添加到list2并返回此元素
用此命令可以实现订单下单流程、用户系统登录注册短信等。
```
### set

```
sadd key value1[value2]：向集合添加成员
scard key：返回集合成员数
smembers key：返回集合中所有成员
sismember key member：判断memeber元素是否是集合key成员的成员
srandmember key [count]：返回集合中一个或多个随机数
srem key member1 [member2]：移除集合中一个或多个成员
spop key：移除并返回集合中的一个随机元素
smove source destination member：将member元素从source集合移动到destination集合
sdiff key1 [key2]：返回所有集合的差集
sdiffstore destination key1[key2]：返回给定所有集合的差集并存储在destination中
```
```
对两个集合间的数据[计算]进行交集、并集、差集运算
1、以非常方便的实现如共同关注、共同喜好、二度好友等功能。对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存储到一个新的集合中。
2、利用唯一性，可以统计访问网站的所有独立 IP
```

### zset
有序且不重复。每个元素都会关联一个double类型的分数，Redis通过分数进行从小到大的排序。分数可以重复

```
ZADD key score1 memeber1
ZCARD key ：获取集合中的元素数量
ZCOUNT key min max 计算在有序集合中指定区间分数的成员数
ZCOUNT key min max 计算在有序集合中指定区间分数的成员数
ZRANK key member：返回有序集合指定成员的索引
ZREVRANGE key start stop ：返回有序集中指定区间内的成员，通过索引，分数从高到底
ZREM key member [member …] 移除有序集合中的一个或多个成员
ZREMRANGEBYRANK key start stop 移除有序集合中给定的排名区间的所有成员(第一名是0)(低到高排序）
ZREMRANGEBYSCORE key min max 移除有序集合中给定的分数区间的所有成员
```
```
常用于排行榜：
1、如推特可以以发表时间作为score来存储
2、存储成绩
3、还可以用zset来做带权重的队列，让重要的任务先执行
```



https://blog.csdn.net/qq_33423418/article/details/101351944

