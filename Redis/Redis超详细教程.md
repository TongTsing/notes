# 	Redis 超详细教程

## 一、安装测试

- 安装： sudo apt-get install redis
- 测试性能： redis-benchmark -q -n 10000
- redis的默认端口号为：6379
- redis没有用户、只有密码

## 二、启动redis服务

```shell
sudo /etc/init.d/redis-server status|start|stop|restart
```

## 三、客户端连接

### 1.redis客户端连接redis服务器

```shell
redis-cli -h ip地址 -p 6379 -a 密码
```

### 2. 测试是否成功连接服务器

```redis
# ping如果返回pong则表示已经连接到redis服务器
ping
```

### 3.redis远程连接

1. 注释掉配置文件中本地ip地址绑定

   ```visual basic
   # bind 127.0.0.1 :: 1
   ```

2. 关闭保护模式

   ```visual basic
   88行 protected-mode no 
   ```

3. 重启服务

   ```shell
   sudo /etc/init.d/redis-server restart
   ```

## 四、redis配置及基础命令

### 1. redis配置文件：

1.  redis配置文件 /etc/redis/redis.conf
2.  redis密码:  requirepass +  -密码

### 2.redis基础命令

- select number: 选择数据库（redis默认有15个数据库， 编号0-14）

- info： 查看redis服务的整体情况

- keys + 正则表达式:

  ```
  # *和？的含义符合正则表达式
  keys * 匹配数据库中所有key
  keys h?llp 匹配hello,hallo等  ?匹配一个任意字符
  ```

- type key :  返回当前key的类型

- exists key: 返回当前键是否存在 1：存在 0：不存在

- del key： 删除key

- rename key newkey：重命名key

- flushdb：清除当前数据库数据

- flushall：清除数据库数据

## 五、redis的数据类型

### 1.字符串类型

- 字符串、数字、都会转换为字符串来存储
- 以二进制的方式存储在内存中

​	**注意：**

​				**key:**

- key命名规范

  可以采用 ----wang：email

- key命名原则

  

  1.不宜过长，过长会消耗内存，而且在数据中查找这类键值的计算成本搞

  2.不宜过短，过短可读性比较差

​				**值：**

- 一个字符串类型的值最多能存储512M的内容

#### 1.1 字符串类型数据常用命令

- **set key value + 可选(nx ex)：** 注释--设置一个类型为字符串的key：value ；nx --> not exist（当key不存在与数据库中时候创建key并且对应的值为value） ；ex-->expire 过期时间，单位s。
- **get key:**  获取key对应的value，返回值为key对应的value 或者'nil'
- **strlen key:**  获取key长度
- **getrange key start stop:**  获取key的指定范围[start, stop]双闭区间切片内容
- **setrange key index value:**  从index开始，使用value替换原内容；并返回最新长度
- **append key value：**在key对应的原value（字符串后面追加一个字符串。与python列表的append方法相同）
- **mset key1 value1 key2 value2 ....：**批量添加key:value
- **mget key1 key2 key3:**  批量获取key1 ....  的值
- **incrby key step：** 将key对应的value增加指定数值
- **decrby key step：** 将key对应的value减少指定数
- **incr key：** +1
- **decr key：** -1
- **incrbyfloat key step：**
- 注意：incr、incrby、decr、decrby、incrbyfloat都只能对value是纯数字的字符串操作，否则报错

#### 1.2 再谈过期时间

##### 1.2.1 设置过期时间

默认情况下，key没有过期时间，需要手动指定

方案1：直接用set的ex参数

方案2：使用expire通用命令

1. set key value
2. expire key time #以秒为单位
3. pexpire key time #以毫秒为单位

##### 1.2.2 查看过期时间

**使用ttl key 命令：**

返回值：

- -1 ：代表当前key没有过期时间
- 大于0： 代表当前key的剩余存活时间
- -2：代表查询的key不存在与数据库中

##### 1.2.3 删除过期时间（把带有过期时间的key变为无过期时间的key）

**使用命令 persist key:**

返回值：

- 1： 代表删除过期时间成功
- 0：代表当前key没有过期时间，或者key不存在于数据库中

#### 1.3 字符串类型的位操作

##### 1.3.1 SETBIT命令

1. 语法格式：setbit key offset value

2. 说明：设置key对应value（value必须是字符串）的位于offset处的二进制值

3. 参数：offset ---偏移量，从0开始   value : 0/1

4. 注意如果offset大于字符串value的长度时，自动在字符串后面补8bit全0，之后再设置offset位

5. SETBIT命令：

   - key不存在：初始化8bit, 全0

   - key存在：1.满足长度要求直接修改位

     ​				2.offset大于value位长时候, 补8bit全0位后再置位

##### 1.3.2 GETBIT命令

1. 语法:  getbit key offset
2. 说明：获取指定位的值

##### 1.3.2 BITCOUNT命令

1. 说明：统计key对应value的位中的1的数量，双闭区间
2. 语法：bitcount key start end



### 2.列表类型

#### 2.1基本概念

1. 元素是字符串类型
2. **列表的头尾增删块，中间增删慢，增删元素是常态（推测是使用双向头尾节点链表）（插入元素顺序与查找结果相反，推测使用头插法建立的链表）**
3. 元素可重复
4. 最多包含2^32-1个元素
5. 索引方式与python列表相同

#### 2.2常用命令

##### 2.2.1 增加数据

1.**Lpush key value1 value2...**

- 说明：从列表头部压入元素
- 返回：list的最新长度

**2.Rpush key value1 value2 ....**

- 说名：从尾部压入元素
- 返回：list的最新长度

**3、RPOPPUSH src dst:**

- 说明：从列表src尾部弹出一个元素、压入到泪飙dst的头部
- 返回值：被弹出的元素

**4、LINSERT key after|before value newvalue**

- 说明：再key对应的列表的指定元素的后/前插入元素
- 返回：
  1. 如果执行成功，返回泪飙的长度
  2. 如果没有找到pivot，返回-1
  3. 如果key不存在或key对应的value为空列表，返回0

##### 2.2.2 查看数据

**1.查看列表中的元素**

LRANGE key start stop

**2. 获取列表长度**

LLEN key

##### 2.2.3 删除列表数据

1. 从列表头部弹出一个元素： LPOP key

2. 从列表尾部弹出要给元素： RPOP key

3. 列表头部阻塞弹出，列表为空时阻塞：BLPOP key timeout

4. 列表尾部阻塞弹出，列表为空时阻塞：BRPOP key timeout

5. 注意：BLPOP 和BRPOP说明：

   1. 如果列表为空或者列表不存在就会阻塞，等待timeout退出或者有数据时候删除数据后退出
   2. 如果timeout = 0 就是永久阻塞、直到有数据可以谈粗
   3. 如果多个客户端阻塞再同一个列表上，使用first in first out原则，先到先服务

   

6. 删除指定元素：LREM key count value

   说明：（LREM---LREMOVE）

   - count > 0: 表示从头开始搜索列表，移除值为value的元素，数量为count
   - count < 0：表示从尾开始搜索列表，移除值为value的元素，数量为count绝对值
   - count = 0:  表示移除列表中索引与value值相等的值
   - 返回值：被实际移除元素的数量

7. 删除质地的那个范围外的元素，保留指定范围内的元素：LTRIM key start stop  [start, stop]双闭区间

   返回值：

   - ok

   场景：保存尾部评论最后500条

   ```sql
   LTRIM weibo:comments 0 499
   ```

### 3.哈希

#### 3.1 基本概念及定义

1. 由field和关联的value组成的键值对
2. field和value是字符串类型
3. 一个hash中最多包含2^32个键值对

#### 3.2 优缺点

- 优点：
  1. 节约内存空间---特定条件下（字段小于512个，value不超过54个字节）
  2. 可按需获取字段的值
- 缺点（不适合hash的情况）
  1. 不能设置value中field字段的过期时间，只能设置最外层key的过期时间
  2. 存储消耗大于字符串结构

#### 3.3hash类型数据常用命令

1. 设置单个字段（field）：

   ```sql
   # 设置key对应的hash值中的file和value
   HSET key field value
   # 当key对应的hash值中不存在field时候，添加field字段并设置field的value
   HSETNX key field value
   ```

2. 设置多个字段

   ```sql
   HMSET key field value field value
   ```

3. 查询key对应hash值的字段个数

   ```sql
   HLEN key
   ```

4. 判断字段是否存在：不存在返回0

   ```sql
   HEXISTS key field
   ```

5. 查询字段值：

   ```sql
   HGET key field
   ```

6. 查询多个字段值

   ```sql
   HMGET key field1, field2...
   ```

7. 查询key对应的值为hash类型的所有键值对（field:value）

   ```sql
   HGETALL key
   ```

8. 查询key对应值为hash类型的数据的所有field

   ```sql
   HKEYS key
   ```

9. 查询所有hash类型的value

   ```sql
   HVALS key
   ```

10. 删除key对应hash值中的filed字段和其对应的value

    ```sql
    HDEL key field
    ```

11. 加/减法：对key对应的hash值的field字段的数值型value做加法加数为increment

    ```sql
    HINCRBY key field increment
    HINCRBYFLOAT key field increment
    HDECRBY key field decrement
    HDECRBYFLOAT key field decrement
    ```


### 4. 集合

#### 4.1 redis数据类型：集合的基本概念

1. 无序、去重
2. 元素是字符串类型
3. 最多包含2^32 -1个元素
4. 类似于python中所学的集合

#### 4.2 redis集合的常用命令

1. 增加一个或者多个元素

   ```sql
   sadd key member1 member2...
   ```

2. 查看集合中所有元素

   ```sql
   smembers key
   ```

3. 删除一个或者多个元素，元素不存在时忽略

   ```sql
   srem key member1 member2...
   ```

4. 判断元素是否存在

   ```sql
   sismember key member
   ```

5. 随机返回集合中count个数的元素，默认为1个

   ```sql
   srandmember key [count]
   ```

6. 弹出成员

   ```sql
   spop key [count]
   ```

7. 返回集合中元素的个数

   ```sql
   scard key
   ```

8. 把元素从源集合移动到目标集合（source 和 destination都是key）

   ```sql
   smove source destination member
   ```

9. 差集（key1 - key2）

   ```sql
   sdiff key1 key2
   ```

10. 差集保存到另一个集合中

    ```sqlite
    sdiffstore destination key1 key2
    ```

11. 交集

    ```sql
    sinter key1 key2
    # 交集保存到destination
    sinterstore destination key1 key2
    ```

12. 并集

    ```sql
    sunion key1 key2
    # 并集结果保存到destination
    sunionstore destination key1 key2
    ```

### 5. 有序集合

#### 5.1 有序集合的基本概念

1. 有序、去重
2. 元素是字符串类型
3. 每个元素都关联着一个浮点分数值(score)，并按照分支从大到小的顺序排列集合中的元素（分支可以相同）
4. 最多包含2^32-1元素

#### 5.2 常用命令

1. 添加成员：返回值为成功插入到集合中的元素的个数

   ```sql
   zadd key score1 member1 score2 member2...
   ```

2. 查看指定区间(这里的区间指的是集合索引区间)元素（升序）：如果不使用可选参数withscores, 则只显示集合的元素（member）不显示member对应的score

   ```sql
   zrange key start stop [withscores]
   
   # 例如:
   zrange key 0 1
   >>>1)'tongqing'
   zrange key 0 1 withscores
   >>>1)'tongqing'
   >>>2)'122'
   ```

3. 查看指定区间（集合索引区间）元素（降序）

   ```sql
   zrevrange key start stop [withscores]
   ```

4. 查看指定区间（scores 的范围）元素

   ```python
   zrangebyscore key min max [withscores] [limit offset count]
   # 双开区间
   zrangebyscore key (min (max [withscores] [limit offset count]
   # 左开区间
   zrangebyscore key (min max withscores [limit offset count]
   # limit offset count
   zrangebyscore key min max withscores limit 0 3#相当于 显示三条符合查询条件的结果
                  
   ```

   - min/max 表示score在区间【min，max】上，默认双闭区间（使用"（min max"可以开启左开右闭区间）
   - limit：与mysql中的limit用法相同
   - offset：跳过多少个元素不显示
   - count：返回元素总数量

5. 查看指定元素的分值

   ```sql
   zscore key member
   ```

6. 删除成员

   ```sql
   zrem key member
   ```

7. 增加或者减少分值

   ```sql
   zincrby key increment member
   zdecrby key decrement member
   ```

8. 返回元素排名（排在升序的第几位，也就相当于是元素的索引值）

   ```sql
   zrank key member
   ```

9. 返回元素逆序排名

   ```sql
   zrevrank key member
   ```

10. 删除score在指定区间内的元素（默认双闭区间，开区间的设置与（4）中相同）

    ```sql
    zrremrangebyscore key min max
    ```

11. 返回集合中元素个数

    ```python
    zcard key
    ```

12. 返回指定score范围中元素的个数

    ```sql
    zcount key min max
    ```

13. 并集

    ```sql
    # 使用weihts参数时候并集元素的score乘以相应weight
    # aggregae参数用来处理多个源集合中元素名相同但score不同（也可以相同）的情况，可以将同名元素相加、取最小值、取最大值： sum|min|max
    zunionstore destination numOfKeys key1 key2...[weights] weight1 weight2... aggregate sum|min|max
    ```

14. 交集同并集

    ```sql
    zinterstore destination numOfkeys key1 key2...[weights] weight1 weight2...aggregate sum|min|max
    ```

    

## 六 、pyredis操作redis(pyredis查询或者pop返回值为元组)

### 1、此操作流程

1. 导入redis库并且创建redis数据库连接对象

   ```python
   import redis
   rds = redis.Redis(host='127.0.0.1', port=6379, db=0, password='1132')
   ```

2. 使用连接对象.redis即可命令操作redis数据库

   ```python
   rds.set(key,value)
   ```

3. 关闭redis连接

   ```python
   rds.close()
   ```


## 七、redis事务

### 1.事务命令

- 开启事务： multi相当于mysql中的begin/start transaction
- 执行命令
- discard：取消该事务中的命令在exec命令之前，类似于mysql中rollback，但mysql中的rollback是撤销已执行的命令，而redis事务中的命令在exec之前并没有执行，而是将命令保存在相应的存储空间中，等待exec命令之后才执行
- exec：相当于mysql中的commit

#### 1.1 redis事务中的乐观锁

- 事务过程中，可以对指定的key进行监听，命令提交时，若事务中某一条命令操作了---已经被另一个命令（这个命令不是当前事务中的命令）修改过的key，则事务不会被执行
- 简而言之：watch key，如果被监控的key在事务执行前被修改则包含对此键key有操作的事务不被执行
- 乐观锁的生存周期为：当一个事务操作了被监控的key，并且成功执行了改事务，则该key的乐观锁被取消，其他key的乐观锁依旧生效

### 2. python中redis流水线

```python
# 导入redis库
# 设置redis连接池的信息
pool = redis.ConnectionPool(host='127.0.0.1', port=6379, db=0, password='1132')
# 创建连接池
r = redis.Redis(connection_poll = pool)
# 利用连接池实例化流水线对象
pipe = r.pipeline()
# 利用流水线存储redis命令
pipe.set('fans',s50)
pipe.incr('fans')
# 提交流水线中的redis命令
pipe.execute()
```

### 3.python操作redis操作事务

```python
# 导入redis库
# 设置redis连接池的信息
pool = redis.ConnectionPool(host='127.0.0.1', port=6379, db=0, password='1132')
# 创建连接池
r = redis.Redis(connection_poll = pool)
with r.pipeline(transaction=true) as pipe:
    pipe.multi()
    pipe.lpush('transction', 1)
    pipe.lpush('transction', 2)
    values = pipe.execute()
```

## 八、redis数据持久化存储

### 1. redis持久化的概念

顾名思义，redis数据持久化过程就是将内存中的redis数据存储到磁盘的过程

### 2.出发redis的rdb持久化



#### 2.1使用save 命令

- save命令完成之前，redis服务器将会被阻塞，任何操作redis服务器的命令都会被阻塞至save完成2.
- 如果RDB文件已经存在，那么服务器会自动使用新的rdb文件替换旧的rdb文件

#### 2.2使用bgsave创建备份文件

​			客户端执行过程如下：

1. 客户端发送bgsave给服务器
2. 服务器马上返回 backgroud saving started给客户端
3. 服务器创建子进程做数据备份
4. 服务器继续给客户端提供服务
5. 子进程备份完RDB文件后再告知**redisfuwuq**

## 九、redis的PUBLISH和SUBSCRIBE

Redis 中的 `PUBLISH` 和 `SUBSCRIBE` 是用于实现发布订阅模式的两个关键命令。

### 1. PUBLISH 命令

`PUBLISH` 命令用于向指定的频道(channel)发布消息。语法如下：

```
PUBLISH channel message
```

- **channel**: 指定消息发布的频道，是一个字符串。
- **message**: 要发布的消息内容，可以是任何字符串。

#### 示例：

假设有一个频道名为 `news`，我们向这个频道发布一条消息：

```
PUBLISH news "Hello, World!"
```

这条命令会将消息 `"Hello, World!"` 发布到 `news` 频道上。

### 2. SUBSCRIBE 命令

`SUBSCRIBE` 命令用于订阅一个或多个频道，接收这些频道发布的消息。语法如下：

```
SUBSCRIBE channel [channel ...]
```

- **channel**: 一个或多个频道名称，以空格分隔。

#### 示例：

我们可以订阅多个频道，比如 `news` 和 `weather`：

```
SUBSCRIBE news weather
```

这条命令会使客户端进入订阅模式，接收 `news` 和 `weather` 两个频道发布的消息。

### 发布-订阅模式的工作方式

- **发布者(Publisher)** 使用 `PUBLISH` 命令向指定频道发布消息。
- **订阅者(Subscriber)** 使用 `SUBSCRIBE` 命令订阅一个或多个频道，以接收发布到这些频道的消息。

#### 通信流程：

1. 发布者向指定频道发布消息。
2. Redis 将消息发送给订阅了该频道的所有客户端。
3. 订阅者接收到消息后，可以进行相应的处理，比如打印、存储等。

#### 注意事项：

- 订阅操作会阻塞连接，直到取消订阅或连接断开。
- 一个客户端可以同时订阅多个频道。
- 发布者和订阅者可以是同一个客户端，可以在同一个连接中同时进行发布和订阅操作。
- 订阅模式在 Redis 中是实时的，订阅者需要保持长连接以接收实时发布的消息。

### 示例场景：

假设有一个即时聊天应用，用户之间的消息通过 Redis 的发布-订阅模式进行传输。每个用户可以订阅属于他们的聊天频道，当其他用户发送消息时，Redis 将实时传递消息给所有订阅了该频道的用户，从而实现即时通讯效果。

通过 `PUBLISH` 和 `SUBSCRIBE` 命令，Redis 提供了一种简单而有效的实现发布-订阅模式的方式，广泛用于实时消息传递、实时数据更新等场景。