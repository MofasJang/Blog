---
title: redis基本数据类型
copyright: false
date: 2022-11-25 14:35:08
tags:
- 数据库
- redis
categories:
- 数据库
- redis
---
# Redis的数据类型及使用
{% note info %}
搬运自CSDN [Redis基本操作命令（图文详解）](https://blog.csdn.net/luna_a/article/details/107292994)
{% endnote %}
　　在服务器上启用redis-server后，在远程客户端使用如下代码连接：
```sh
redis-cli(或redis-cli.exe) -h host -p port -a password
```
<!-- more -->
## 基本命令
|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`dbsize`|查看当前数据库中key的数量|
|`select db`|切换库命令,下标0-15|
|`flushdb`|删除当前数据库的所有数据|
|`keys pattern`|查看数据库所有符合pattern的key|
|`exists key [key…]`|判断key是否存在|
|`expire key seconds`|设置 key 的生存时间，超过时间，key 自动删除。单位是秒。|
|`ttl key`|以秒为单位，返回 key 的剩余生存时间（ttl: time to live）|
|`type key`|查看 key 所存储值的数据类型|
|`del key [key…]`|删除存在的 key，不存在的 key 忽略。|

## 字符串（String）

### 基本命令

|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`set key value`|把value的值赋给key|
|`get key`|获取key的value|
|`incr key`|将 key 中储存的数字值加 1，如果 key 不存在，则 key 的值先被初始化为 0 再执行incr 操作|
|`decr key`|将 key 中储存的数字值减1，如果 key 不存在，则么 key 的值先被初始化为 0 再执行 decr 操作|
|`append key value`|如果 key 存在，则将 value 追加到 key 原来旧值的末尾<br/>如果 key 不存在，则将 key 设置值为 value|

### 常用命令
|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`strlen key`|返回key存储的value的总长度，key不存在返回0|
|`getrange key start end`|获取 key 中字符串值从 start 开始到 end 结束的子字符串,包括 start 和 end|
|`setrange key offset value`|用 value 覆盖（替换）key 的存储的值从 offset 开始,不存在的 key 做空白字符串。|
|`mset key value [key value…]`|同时设置一个或多个 key-value 对|
|`mget key [key …]`|获取所有(一个或多个)给定 key 的值|

## 哈希表（Hash）

### 基本命令

|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`hset hash表的key field value`|将哈希表 key 中的域 field 的值设为 value，如果 key 不存在，则新建 hash 表，执行赋值，如果有 field ,则覆盖值。|
|`hget key field`|获取哈希表 key 中给定域 field 的值|
|`hmset key field value [field value…]`|同时将多个 field-value (域-值)设置到哈希表 key 中，此命令会覆盖已经存在的 field，hash 表 key 不存在，创建空的 hash 表，执行 hmset.|
|`hmget key field [field…]`|获取哈希表 key 中一个或多个给定域的值|
|`hgetall key`|获取哈希表 key 中所有的域和值|
|`hdel key field [field…]`|删除哈希表 key 中的一个或多个指定域 field，不存在 field 直接忽略|

### 常用命令

|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`hkeys key`|查看哈希表 key 中的所有 field 域|
|`hvals key`|返回哈希表 key 中所有域的值|
|`hexists key field`|查看哈希表 key 中，给定域 field 是否存在|

## 列表（List）

### 基本命令
|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`lpush key value [value…]`|将一个或多个值 value 插入到列表 key 的表头（最左边），从左边开始加入值，从左到右的顺序依次插入到表头|
|`rpush key value [value…]`|将一个或多个值 value 插入到列表 key 的表尾（最右边），各个 value 值按从左到右的顺序依次插入到表尾|
|`lrange key start stop`|获取列表 key 中指定区间内的元素，0 表示列表的第一个元素，以 1 表示列表的第二个元素；start , stop 是列表的下标值，也可以负数的下标， -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。start ，stop 超出列表的范围不会出现错误。|
|`lindex key index`|获取列表 key 中下标为指定 index 的元素，列表元素不删除，只是查询。|
|`llen key`|获取列表 key 的长度|

### 常用命令

|<div style="width:250px">语法</div>|功能|
|:------------:|:-:|
|`lrem key count value`|根据参数 count 的值，移除count个 列表中与参数 value 相等的元素，count >0 ，从列表的左侧向右开始移除；count < 0 从列表的尾部开始移除；count = 0 移除表中所有与 value 相等的值。|
|`lset key index value`|将列表 key 下标为 index 的元素的值设置为 value。|
|`linsert key BEFORE|AFTER pivot value`|将值 value 插入到列表 key 当中位于值 pivot 之前或之后的位置。key 不存在，pivot不在列表中，不执行任何操作。|

## 无序集合（Set）

### 基本命令
| <div style="width:250px">语法</div> |                             功能                             |
| :---------------------------------: | :----------------------------------------------------------: |
|     `sadd key member [member…]`     | 将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略，不会再加入。 |
|           `smembers key`            |    获取集合 key 中的所有成员元素，不存在的 key 视为空集合    |
|       `sismember key member`        | 判断 member 元素是否是集合 key 的成员<br/>返回值：member 是集合成员返回 1，其他返回 0 。 |
|             `scard key`             |                    获取集合里面的元素个数                    |
|     `srem key member [member…]`     | 删除集合 key 中的一个或多个 member 元素，不存在的元素被忽略。 |

### 常用命令

| <div style="width:250px">语法</div> |                             功能                             |
| :---------------------------------: | :----------------------------------------------------------: |
|      `srandmember key [count]`      | 只提供 key，随机返回集合中一个元素，元素不删除，依然在集合中；提供了 count时，count 正数, 返回包含 count 个数元素的集合，集合元素各不相同，最多返回全部key中元素。count 是负数，返回一个 count 绝对值的长度的集合，集合中元素可能会重复多次。 |
|         `spop key [count]`          | 随机从集合中删除一个元素, count 是删除的元素个数。<br/>返回值：被删除的元素，key 不存在或空集合返回 nil |

## 有序集合（Zset）

### 基本命令
|   <div style="width:250px">语法</div>   |                             功能                             |
| :-------------------------------------: | :----------------------------------------------------------: |
| `zadd key score member [score member…]` | 将一个或多个 member 元素及其 score 值加入到有序集合 key 中，如果 member 存在集合中，则更新值；score 可以是整数或浮点数 |
|  `zrange key start stop [WITHSCORES]`   | 查询有序集合，指定区间的内的元素。集合成员按 score 值从小到大来排序。start，stop 都是从 0 开始。0 是第一个元素，1 是第二个元素，依次类推。以 -1 表示最后一个成员，-2 表示倒数第二个成员。WITHSCORES 选项让 score 和 value 一同返回。 |
| `zrevrange key start stop [WITHSCORES]` | 返回有序集 key 中，指定区间内的成员。其中成员的位置按 score 值递减(从大到小)来排列。其它同 zrange 命令。 |
|       `zrem key member [member…]`       |   删除有序集合 key 中的一个或多个成员，不存在的成员被忽略    |
|               `zcard key`               |               获取有序集 key 的元素成员的个数                |

### 常用命令

|             <div style="width:250px">语法</div>              |                             功能                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| `zrangebyscore key min max [WITHSCORES ] [LIMIT offset count]` | 获取有序集 key 中，所有 score 值介于 min 和 max 之间（包括 min 和 max）的成员，有序成员是按递增（从小到大）排序。min ,max 是包括在内，使用符号( 表示不包括。 min， max 可以使用 -inf ，+inf 表示最小和最大。 limit 用来限制返回的起始点和数量。 |
| `zrevrangebyscore key max min [WITHSCORES ] [LIMIT offset count]` | 返回有序集 key 中， score 值介于 max 和 min 之间(默认包括等于 max 或 min )的所有的成员。有序集成员按 score 值递减(从大到小)的次序排列。 |
|                     `zcount key min max`                     | 返回有序集 key 中，score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max )的成员的数量 |