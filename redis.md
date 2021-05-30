# redis

## 概念

redis是一款高性能的nosql系列的非关系型数据库。

	1. 官网：https://redis.io
	2. 中文网：http://www.redis.net.cn/
## redis的应用场景

​		1.缓存（数据查询、短链接、新闻内容、商品内容等）

​		2.聊天室的在线好友列表

​		3.任务队列（秒杀，抢购、12306等）

​		4.应用排行版

​		5.网站访问统计

​		6.数据过期处理（能够精确到毫秒）

​		7.分布式集群架构中的session分离

redis是使用C语言开发的一个开源高性能键值对（key-value）数据库，官方提供测试数据，50个并发执行100000个请求，读的速度是110000次/s，写的速度是81000次/s，且redis通过提供多种键值数据类型来适应不同场景下的存储需求，

## redis的数据类型

 redis的数据结构：

		* redis存储的是：key,value格式的数据，其中key都是字符串，value有5种不同的数据结构

​		1.字符串类型 string

​		2.哈希类型  hash    map格式 

​		3.列表类型  list      linkedlist格式。支持重复元素

​		4.集合类型  set       不允许重复元素（无序）

​		5.有序集合类型 sortedset   不允许重复元素，且元素有顺序



### **String**

```bash
myredis1:0>set key1 v1    #设置值
"OK"
myredis1:0>get key1       #获取值
"v1" 
myredis1:0>type key1       #获取key的类型
"string"
myredis1:0>exists key1      #判断某一个key是否存在
"1"
myredis1:0>append key1 "hello"   #追加一个字符串，如果key不存在，									相当于set key
"7"
myredis1:0>get key1
"v1hello"
myredis1:0>strlen key1     #获取此key中value的长度
"7"
myredis1:0>set views 0
"OK"
myredis1:0>get views
"0"
myredis1:0>incr views     # 自增一
"1"
myredis1:0>incr views
"2"
myredis1:0>get views
"2"
myredis1:0>decr views   #自减一
"1"
myredis1:0>decr views
"0"
myredis1:0>del views    #删除一个key
"1"
myredis1:0>incrby views 10   #增加指定步长
"10"
myredis1:0>get views
"10"
myredis1:0>decrby views 5       #自减指定步长
"5"
myredis1:0>set key1 "hello,world"
"OK"
myredis1:0>get key1
"hello,world"
myredis1:0>getrange key1 0 3    #截取指定区间[0,3]字符串
"hell"
myredis1:0>getrange key1 0 -1   #获取全部字符串与get key相同
"hello,world"
myredis1:0>setrange key1 1 44    #替换指定位置开始的字符串
"11"
myredis1:0>get key1
"h44lo,world"

**********************************************************
myredis1:0>setex key 30 "hello,world"   #设置key的过期时间
"OK"
myredis1:0>ttl key   #查看剩余key的过期时间
"25"
myredis1:0>ttl key
"20"
myredis1:0>ttl key
"16"
myredis1:0>ttl key   #key过期后显示-2
"-2"
myredis1:0>setnx key2 "hello"   #不存在将设置此key（分布式锁中常常使                                  用）如果key2不存在，创建key2
"1"
myredis1:0>keys *
 1)  "key2"
 2)  "key1"
myredis1:0>setnx key2 "world"      #如果key2存在，创建失败
"0"
myredis1:0>get key2
"hello"
**********************************************************
myredis1:0>mset k1 v1 k2 v2 k3 v3   #批量设置key-value
"OK"
myredis1:0>keys *
 1)  "k3"
 2)  "k1"
 3)  "key1"
 4)  "k2"
 5)  "key2"
 myredis1:0>mget k1 k2 k3  #同时获取多个值
 1)  "v1"
 2)  "v2"
 3)  "v3"
 myredis1:0>msetnx k4 v4 k5 v5  #msetnx是一个原子操作，要么一起成								功，要么一起失败
"1"
myredis1:0>msetnx k1 v1 k6 v6
"0"
**********************************************************
 设置一个user:1对象 值为json字符来保存一个对象
myredis1:0>set user:1 {name:zhangsan,age:20}
"OK"
这里的key是一个巧妙的设计：user：{id}:{filed},如此设计在redis中也可以成功
 myredis1:0>mset user:1:name shangsan user:1:age 30
"OK"
myredis1:0>mget user:1:name
 1)  "shangsan"
 myredis1:0>getset db redis   #先get再set，如果不存在值，则返回null，再设置值，如果这个值存在，获取原来的值，再设置新的值
null
myredis1:0>get db
"redis"
```

String类型的应用场景：

​		1.计数器

​		2.统计多单位的数量，

​		3.粉丝数

​		4，对象缓存存储

### List

可当作队列、栈、阻塞队列

![image-20201114105249339](C:\Users\RaoWQ\AppData\Roaming\Typora\typora-user-images\image-20201114105249339.png)

所有命令都是以l开头

```bash
myredis1:0>lpush list one   将一个值或者多个值插入到列表的头部
"1"
 myredis1:0>lrange list 0 -1
 1)  "two"
 2)  "one"
 3)  "three"
myredis1:0>lpop list   #弹出某个值（移除列表的第一个元素从左）
"two"
myredis1:0>rpop list  #弹出某个值（移除列表的第一个元素从右）
"three"
myredis1:0>lindex list 0   #通过下标获得list中的某个值
"one"
myredis1:0>llen list    #获取list的长度
"1"
myredis1:0>lrem key 1 one   #移除列表中指定个数的值
"0"
myredis1:0>lrange list 0 -1
 1)  "test"
 2)  "one"
myredis1:0>ltrim list 0 1  #截取指定区间的值[0,1],只剩下此范围的值
"OK"
myredis1:0>lrange list 0 -1
 1)  "test"
 2)  "one"
 **********************************************************
# lpoplpush  移除列表的最后一个元素到一个新的列表中
 myredis1:0>rpoplpush list mylist
"one"
myredis1:0>lrange list 0 -1
 1)  "test"
myredis1:0>lrange mylist 0 -1
 1)  "one"
 myredis1:0>exists list   #判断此列表是否存在
"0"
myredis1:0>lpush list value1
"1"
myredis1:0>lset list 0 item   #将列表中指定下标的值替换为另一个值更新操作，如果列表不存在将报错，如果存在，将更新
"OK"
myredis1:0>lrange list 0 -1
 1)  "item"
 **********************************************************
 myredis1:0>LINSERT list before "item" "ttttt"  #向item之前插入											一个元素
"2"
myredis1:0>lrange list 0 -1
 1)  "ttttt"
 2)  "item"
 myredis1:0>LINSERT list after "item" "aaaa"       #向item之后插入											一个元素
"3"
myredis1:0>lrange list 0 -1
 1)  "ttttt"
 2)  "item"
 3)  "aaaa"
```

list实际上相当于是一个链表，

1.如果key不存在，创建新的链表

2.如果key存在，新增内容

3.如果移除了所有值，空链表，也代表不存在！

4.在两边插入或者改动值，效率高！中间元素，效率低下

**应用场景：**

​	消息排队！消息队列  ，栈等

### Set

set中的值是无序且不能重复的

```bash
myredis1:0>sadd mmyset "hello"   #给key添加一个值
"1"
myredis1:0>sadd mmyset "world"
"1"
myredis1:0>smembers mmyset      #查看key中的值
 1)  "world"
 2)  "hello"
myredis1:0>sismember mmyset "hello"   #在key中查看指定的值是否存在
"1"
myredis1:0>scard mmyset     #获取此key的元素的个数
"2"
**********************************************************
myredis1:0>srem mmyset hello   #移除key中指定的值
"1"
myredis1:0>smembers mmyset
 1)  "world"
myredis1:0>srandmember mmyset   #从集合中随机取出一个值
"hello"
myredis1:0>srandmember mmyset
"aaaa"
myredis1:0>srandmember mmyset 2  #从集合中随机取出多个值
 1)  "hello"
 2)  "aaaa"
myredis1:0>srandmember mmyset 2
 1)  "world"
 2)  "aaaa"
 **********************************************************
 myredis1:0>spop mmyset    #随机移除集合中一个值
"hello" 
myredis1:0>smove mmyset myset "world"   #将集合中指定的值移动到另一											个集合中去
"1"
**********************************************************
myredis1:0>sdiff key1 key2   #两个集合的差集key1-key2
 1)  "b"
 2)  "a"
myredis1:0>sinter key1 key2  #两个集合的交集（可用与共同好友）
 1)  "c"
myredis1:0>sunion key1 key2   #两个集合的并集
 1)  "e"
 2)  "a"
 3)  "c"
 4)  "b"
 5)  "d"

```

微博，A用户将所有关注的人放在一个set集合中，将它的粉丝也放在一个集合中！   共同关注，共同爱好，二度好友，推荐好友！

### Hash

Map集合 ，key-map（即value是一个map集合）

```bash
myredis1:0>hset myhash field1 rao  #添加一个值
"1"
myredis1:0>hget myhash field1   #获取一个值
"rao"
myredis1:0>hmset myhash field1 hello1 field2 worlds   #添加多个							key-value
"OK"
myredis1:0>hgetall myhash
 1)  "field1"
 2)  "hello1"
 myredis1:0>hdel myhash field1   #删除一个值
"1"
myredis1:0>hlen myhash   #查看此key的长度
"0"
myredis1:0>hexists myhash field1   #查看此值是否在此hash中
"0"
```

### Zset

有序集合

```bash
myredis1:0>zadd myset 1 one  #在指定位置添加一个值
"1"
myredis1:0>zadd myset 2 two 3 three   #添加多个值
"2" 
myredis1:0>zrange myset 0 -1   #查看多个值
 1)  "one"
 2)  "two"
 3)  "three"
 myredis1:0>zrange myset 0 -1 withscores  #查看设置的位置
 1)  "one"
 2)  "1"
 3)  "two"
 4)  "2"
 5)  "three"
 6)  "3"
 7)  "test"
 8)  "8"
 myredis1:0>zrem myset one  #删除：zrem key value
"1"
```

应用场景：

​		set排序，存储班级成绩表，工资表排序

​		普通消息，1，重要消息，2，带权重进行判断

​		排行榜应用

```bash
 通用命令
		1. keys * : 查询所有的键
		2. type key ： 获取键对应的value的类型
		3. del key：删除指定的key value
```



## 三种特殊数据类型



### geospatial

​		朋友的定位，附件的人，打车距离的计算

​		redis的Geo在redis3.2版本就推出了，这个功能可以推算出地理位置信息，两地之间的距离以及方圆几里的人！

只有六个命令：

```bash
GEOADD   #添加地理位置  两极无法直接添加，先下载数据，通过Java程序导入
GEODIST
GEOHASH
GEOPOS
GEORADIUS
GEORADIUSBYMEMBER
```

**自3.2.0起可用。**

**时间复杂度：****时间复杂度： O（log（N）），其中N是排序集中的元素数。

将指定的地理空间项（纬度，经度，名称）添加到指定的键。数据以排序集的形式存储在密钥中，该方式使得以后可以使用[GEORADIUS](https://redis.io/commands/georadius)或[GEORADIUSBYMEMBER](https://redis.io/commands/georadiusbymember)命令通过半径查询来检索项目。

该命令采用标准格式x，y的参数，因此必须在纬度之前指定经度。可以分度的坐标有一些限制：极点附近的区域无法分度。由EPSG：900913 / EPSG：3785 / OSGEO：41001指定的确切限制如下：

- 有效经度为-180至180度。
- 有效纬度为-85.05112878至85.05112878度。

当用户尝试索引超出指定范围的坐标时，该命令将报告错误。

**注意：**没有**GEODEL**命令，因为可以使用[ZREM](https://redis.io/commands/zrem)来删除元素。地理位置索引结构只是一个排序集。

```bash
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEODIST Sicily Palermo Catania
"166274.15156960039"
redis> GEORADIUS Sicily 15 37 100 km   
1) "Catania"
redis> GEORADIUS Sicily 15 37 200 km
1) "Palermo"
2) "Catania"

myredis1:0>geopos china:city beijin   #获取定位信息
 1)    1)   "116.39999896287918091"
  2)   "39.90000009167092543"

myredis1:0>GEODIST china:city beijin shanghai #查看两个城市的距离（单位m）
"1067378.7564"
myredis1:0>GEODIST china:city beijin shanghai km   #查看两个城市的距离 （单位km）
"1067.3788"
myredis1:0>georadius china:city 110 30 1000 km   #以此经纬度为中心，寻找方圆1000km的城市
**********************************************************
GEOADD
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEODIST Sicily Palermo Catania
"166274.15156960039"
redis> GEORADIUS Sicily 15 37 100 km
1) "Catania"
redis> GEORADIUS Sicily 15 37 200 km
1) "Palermo"
2) "Catania"
redis> 
**********************************************************
GEOHASH 
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEOHASH Sicily Palermo Catania
1) "sqc8b49rny0"
2) "sqdtr74hyu0"
**********************************************************
GEODIST
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEODIST Sicily Palermo Catania
"166274.15156960039"
redis> GEODIST Sicily Palermo Catania km
"166.27415156960038"
redis> GEODIST Sicily Palermo Catania mi
"103.31822459492736"
redis> GEODIST Sicily Foo Bar
(nil)
redis> 
**********************************************************
GEOPOS 
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEOPOS Sicily Palermo Catania NonExisting
1) 1) "13.361389338970184"
   2) "38.115556395496299"
2) 1) "15.087267458438873"
   2) "37.50266842333162"
3) (nil)
**********************************************************
GEORADIUS
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEORADIUS Sicily 15 37 200 km WITHDIST  #显示到中心距离的位置
1) 1) "Palermo"
   2) "190.4424"
2) 1) "Catania"
   2) "56.4413"
redis> GEORADIUS Sicily 15 37 200 km WITHCOORD  #显示城市的经纬度定位
1) 1) "Palermo"
   2) 1) "13.36138933897018433"
      2) "38.11555639549629859"
2) 1) "Catania"
   2) 1) "15.08726745843887329"
      2) "37.50266842333162032"
redis> GEORADIUS Sicily 15 37 200 km WITHDIST WITHCOORD
1) 1) "Palermo"
   2) "190.4424"
   3) 1) "13.36138933897018433"
2) 1) "Catania"
   2) "56.4413"
   3) 1) "15.08726745843887329"
      2) "37.50266842333162032"
**********************************************************
GEORADIUSBYMEMBER
redis> GEOADD Sicily 13.583333 37.316667 "Agrigento"
(integer) 1
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEORADIUSBYMEMBER Sicily Agrigento 100 km
1) "Agrigento"
2) "Palermo"
redis> 
```

### HyperLogLog 

Redis 在 2.8.9 版本添加了 HyperLogLog 结构。

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

**什么是基数？**

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。



#### Redis HyperLogLog 命令

下表列出了 redis HyperLogLog 的基本命令：

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [PFADD key element [element ...\]](https://www.redis.net.cn/order/3629.html) 添加指定元素到 HyperLogLog 中。 |
| 2    | [PFCOUNT key [key ...\]](https://www.redis.net.cn/order/3630.html) 返回给定 HyperLogLog 的基数估算值。 |
| 3    | [PFMERGE destkey sourcekey [sourcekey ...\]](https://www.redis.net.cn/order/3631.html) 将多个 HyperLogLog 合并为一个 HyperLogLog |

```bash
redis 127.0.0.1:6379> PFADD w3ckey "redis"
1) (integer) 1
redis 127.0.0.1:6379> PFADD w3ckey "mongodb"
1) (integer) 1
redis 127.0.0.1:6379> PFADD w3ckey "mysql"
1) (integer) 1
redis 127.0.0.1:6379> PFCOUNT w3ckey
(integer) 3
**********************************************************
PFMERGE
redis 127.0.0.1:6379> PFADD hll1 foo bar zap a
(integer) 1
redis 127.0.0.1:6379> PFADD hll2 a b c foo
(integer) 1
redis 127.0.0.1:6379> PFMERGE hll3 hll1 hll2
OK
redis 127.0.0.1:6379> PFCOUNT hll3
(integer) 6
redis> 
```

### Bitmaps

位图不是实际的数据类型，而是在String类型上定义的一组面向位的操作。由于字符串是二进制安全Blob，并且最大长度为512 MB，因此它们适合设置多达2 32个不同的位。

位操作分为两类：固定时间的单个位操作（如将一个位设置为1或0或获取其值），以及对位组的操作，例如计算给定位范围内设置的位的数量（例如，人口计数）。

位图的最大优点之一是，它们在存储信息时通常可以节省大量空间。例如，在以增量用户ID表示不同用户的系统中，仅使用512 MB内存就可以记住40亿用户的一位信息（例如，知道用户是否要接收新闻通讯）。

使用[SETBIT](https://redis.io/commands/setbit)和[GETBIT](https://redis.io/commands/getbit)命令设置和检索位：

```
> setbit key 10 1
(integer) 1
> getbit key 10
(integer) 1
> getbit key 11
(integer) 0
```

所述[SETBIT](https://redis.io/commands/setbit)命令采用作为第一个参数的比特数，和作为第二个参数的值以设置所述位，其为1或0的命令自动放大字符串，如果寻址位是当前字符串长度之外。

[GETBIT](https://redis.io/commands/getbit)只是返回指定索引处的位的值。超出范围的位（寻址超出存储在目标键中的字符串长度的位）始终被视为零。

在位组上有三个命令：

1. [BITOP](https://redis.io/commands/bitop)在不同的字符串之间执行按位运算。提供的运算为AND，OR，XOR和NOT。
2. [BITCOUNT](https://redis.io/commands/bitcount)执行填充计数，报告设置为1的位数。
3. [BITPOS](https://redis.io/commands/bitpos)查找指定值为0或1的第一位。

无论[BITPOS](https://redis.io/commands/bitpos)和[比特计数](https://redis.io/commands/bitcount)能够与字符串的字节范围进行操作，而不是该字符串的整个长度运行。以下是[BITCOUNT](https://redis.io/commands/bitcount)调用的一个简单示例：

```bash
> setbit key 0 1
(integer) 0
> setbit key 100 1
(integer) 0
> bitcount key
(integer) 2
```

位图的常见用例是：

- 各种实时分析。
- 统计用户信息，活跃，不活跃！登录、未登录！打卡
- 存储与对象ID相关联的空间高效但高性能的布尔信息。

例如，假设您想知道网站用户每天访问量最长的时间。您开始从零开始计数，即从您公开网站的那一天开始，并在用户每次访问该网站时对[SETBIT进行](https://redis.io/commands/setbit)设置。作为位索引，您只需花费当前的unix时间，减去初始偏移量，然后除以一天中的秒数（通常为3600 * 24）。

这样，对于每个用户，您都有一个小的字符串，其中包含每天的访问信息。使用[BITCOUNT](https://redis.io/commands/bitcount)，可以轻松获得给定用户访问该网站的天数，而只需几个[BITPOS](https://redis.io/commands/bitpos)调用，或者仅获取并分析客户端的位图，就可以轻松计算出最长的条纹。

位图很容易拆分成多个键，例如，为了分片数据集，因为通常最好避免使用大键。要在不同的密钥上拆分位图，而不是将所有位都设置为密钥，一个简单的策略就是为每个密钥存储M位，并使用来获取密钥名称，使用来获取`bit-number/M`第N位`bit-number MOD M`。

## Redis 事务

redis事务没有隔离级别概念！redis单条命令是保证原子性，但是redis事务不保证原子性

Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：

- 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

一个事务从开始到执行会经历以下三个阶段：

- 开始事务。（multi）
- 命令入队。(get\set\sadd等)
- 执行事务。(EXEC)

------

### 实例

以下是一个事务的例子， 它先以 **MULTI** 开始一个事务， 然后将多个命令入队到事务中， 最后由 **EXEC** 命令触发事务， 一并执行事务中的所有命令：

```bash
redis 127.0.0.1:6379> MULTI
OK
redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"
QUEUED
redis 127.0.0.1:6379> GET book-name
QUEUED
redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"
QUEUED
redis 127.0.0.1:6379> SMEMBERS tag
QUEUED
redis 127.0.0.1:6379> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
   2) "C++"
   3) "Programming"
```

------

### 命令

下表列出了 redis 事务的相关命令：

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [DISCARD](https://www.redis.net.cn/order/3638.html) 取消事务，放弃执行事务块内的所有命令。 |
| 2    | [EXEC](https://www.redis.net.cn/order/3639.html) 执行所有事务块内的命令。 |
| 3    | [MULTI](https://www.redis.net.cn/order/3640.html) 标记一个事务块的开始。 |
| 4    | [UNWATCH](https://www.redis.net.cn/order/3641.html) 取消 WATCH 命令对所有 key 的监视。 |
| 5    | [WATCH key [key ...\]](https://www.redis.net.cn/order/3642.html) 监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。 |

1.如果发现事务执行失败，就先解锁

2.获取最新的值，再次监视，

3.比对监视的值是否发生了变化，如果没有变化，那么就可以执行成功，如果改变就执行失败

## java直接连接redis

### 连接到 redis 服务

```java
import redis.clients.jedis.Jedis;
public class RedisJava {
   public static void main(String[] args) {
      //连接本地的 Redis 服务
      Jedis jedis = new Jedis("localhost");
      System.out.println("Connection to server sucessfully");
      //查看服务是否运行
      System.out.println("Server is running: "+jedis.ping());
 }
}
```

编译以上 Java 程序，确保驱动包的路径是正确的。

```java
$javac RedisJava.java
$java RedisJava
Connection to server sucessfully
Server is running: PONG
Redis Java String Example
```

------

### Redis Java String(字符串) 实例

```java
import redis.clients.jedis.Jedis;
public class RedisStringJava {
   public static void main(String[] args) {
      //连接本地的 Redis 服务
      Jedis jedis = new Jedis("localhost");
      System.out.println("Connection to server sucessfully");
      //设置 redis 字符串数据
      jedis.set("w3ckey", "Redis tutorial");
     // 获取存储的数据并输出
     System.out.println("Stored string in redis:: "+ jedis.get("w3ckey"));
 }
}
```

编译以上程序。

```java
$javac RedisStringJava.java
$java RedisStringJava
Connection to server sucessfully
Stored string in redis:: Redis tutorial
```

------

### Redis Java List(列表) 实例

```java
import redis.clients.jedis.Jedis;
public class RedisListJava {
   public static void main(String[] args) {
      //连接本地的 Redis 服务
      Jedis jedis = new Jedis("localhost");
      System.out.println("Connection to server sucessfully");
      //存储数据到列表中
      jedis.lpush("tutorial-list", "Redis");
      jedis.lpush("tutorial-list", "Mongodb");
      jedis.lpush("tutorial-list", "Mysql");
     // 获取存储的数据并输出
     List<String> list = jedis.lrange("tutorial-list", 0 ,5);
     for(int i=0; i<list.size(); i++) {
       System.out.println("Stored string in redis:: "+list.get(i));
     }
 }
}
```

编译以上程序。

```java
$javac RedisListJava.java
$java RedisListJava
Connection to server sucessfully
Stored string in redis:: Redis
Stored string in redis:: Mongodb
Stored string in redis:: Mysql
```

------

### Redis Java Keys 实例

```java
import redis.clients.jedis.Jedis;
public class RedisKeyJava {
   public static void main(String[] args) {
      //连接本地的 Redis 服务
      Jedis jedis = new Jedis("localhost");
      System.out.println("Connection to server sucessfully");
      
     // 获取数据并输出
     List<String> list = jedis.keys("*");
     for(int i=0; i<list.size(); i++) {
       System.out.println("List of stored keys:: "+list.get(i));
     }
   }
}
```

编译以上程序。

```java
$javac RedisKeyJava.java
$java RedisKeyJava
Connection to server sucessfully
List of stored keys:: tutorial-name
List of stored keys:: tutorial-list
```