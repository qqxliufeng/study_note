## redis学习笔记

### 键

key

### 基本数据类型

+ string

+ list

  ***Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）***

+ hash

  ***Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象***

+ set

  ***Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。***

  ***Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)***

+ scoreset

  ***Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。***

  ***不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。***

  ***有序集合的成员是唯一的,但分数(score)却可以重复。***

  ***集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。 集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。***

### key

> del key //删除key
>
> keys * // 获取所有的key
>
> exists key // 判断key 是否存在
>
> expire key seconds // 设置key的过期时间，以秒为单位
>
> ttl key // 返回key 还有多少时间过期，以秒为单位
>
> type key // 返回key的类型 

### 1. string

 #### 存储:  

> 存储带有过期时间：setex key1 10 value1 // 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。
>
> 存储如果key 不存在的情况： setnx key value // 只有在 key 不存在时设置 key 的值
>
> 自增 1/自减 1：incr key / decr key
>
> 自增 n / 自减 n: incrby key number / decrby key number

### 获取：

> get key // 如果key 不存在则会直接返回：nil
>
> getset key newValue // 获取旧值并设置新值
>
> strlen key // 获取字符的长度

### 2. list

#### 存储 (增)

> lpush k v1 v2 v3 ... vn // 存储多个值，以栈的形式，先进后出
>
> rpush k v1 v2 v3 ... vn // 存储多个值 ，以队列的形式，先进先出
>
> linster k before|after pivot value // 从左边开始，在指定值前/后面插入新值

#### 删除 (删)

> lpop rpop 同下
>
> Lrem count value // 移除多少个相同的元素
>
> ltrim key start end // 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。

#### 更新 (改)

> lset kl index value // 对一个列表索引为index的设置新值

#### 获取 (查)

> lpop k // 移出并获取最前面的元素
>
> rpop k //移出并获取最后面的元素
>
> lindex k index // 获取索引为index的元素
>
> llen kl // 获取list长度 
>
> lrange k start end // 获取从start到end的范围元素, 0 -1 是获取全部的

### hash

#### 新增（增）、更新（改）

> hset key field value // 给key设置一个键值对
>
> hmset key field1 value1 field2 value2 [fieldn valuen] // 同时设置多个键值对

#### 删除（删）

> Hdel key field [fieldn] // 删除键值对

#### 获取（查）

> hget key field // 根据键获取值
>
> hmget key field [fieldn] // 获取多个键的值
>
> hgetall key // 获取所有的键值
>
> hkeys key // 获取所有的键
>
> hvals key // 获取所有的值
>
> Hlen key // 获取hash长度

### set

#### 新增（增）

> sadd key member1 member2 member3 .... membern // 往集合中添加多个元素值

#### 删除（删）

> srem key member1 member2 member3 .... membern // 移除集合中的成员，有就移除，没有就算了
>
> spop key // 随机移除一个成员并返回

#### 多个集合的并、差、补、移动

> sdiff k1 k2 // 以k1 为基础，k2为参照，返回k1中不在k2中的成员（差）
>
> sinter k1 k2 // 返回两个集合中相同的成员（交）
>
> sunion k1 k2 // 返回两个集合中所有的成员，去除重复的成员 （并）
>
> smove k_source k_distination member // 把k_source集合中的成员member 移动到集合k_distination中

#### 获取（查）

> scard key // 获取成员的个数
>
> smembers key // 获取所有的成员
>
> Sismember key member // 判断member是否在集合中，存在则返回 1 不存在则返回 0

### scoreset

#### 新增（增）

> zadd key score member1 score member2 score member3 .... score membern // 往集合中添加多个成员，以score进行排序

#### 删除（删）

> zrem key member [memebern] // 移除集合中的成员
>
> zremrangebyscore key min max // 移除集合中的给定的分数区间的所有成员 

### 获取、排序（查）

> zrange key start stop [withscores] //通过索引获取从start 到 stop之间的成员，如果有withscores 则把score也返回，如果没有则直接返回成员，如果start=0 stop=-1，则返回所有，从低到高
>
> zrevrange key star stop [withscores] //通过索引获取从start 到 stop之间的成员，如果有withscores 则把score也返回，如果没有则直接返回成员，如果start=0 stop=-1，则返回所有，从高到低

