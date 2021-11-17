# Redis:Nosql数据库

### 一、非关系型数据库

- 大部分的非关系型数据库是以文档形式存储，列表，字典，键值对
- 不支持SQL语法，读写性能高，灵活的数据模型
- 存储热点数据，可以并发高速读取。

### 二、redis基础

- redis-server：启动redis服务，

- redis-cli SHUTDOWN：关闭服务

- 端口：6379——配置文件在/etc/redis/redis.conf中

- redis-cli  -h 127.0.0.1 -p 6379——远程连接redis数据库

- redis-cli——本地连接，ping——查看是否正常，推荐大写
- echo hello——输出hello

### 三、redis的使用

1. 存在16个库，从0开始递增，0-15个数据库名。自动选择0号数据库，select 1——进入1好数据库
2. 一个项目对应一个redis实例，0-15个数据库
3. set num 1——设置num:1，GET num——得到1，keys * ——查看全部key，mset  key1 value1 key2 value2 ——设置多个值
4. mget key1 key2 ——得到多个值，keys num?——问号表示任意一个字符，keys num[0-9]——获得num0到9
5. exists key ——判断是否存在，存在返回1，不存在返回0，rename key1 key2——对key进行改名
6. expire key 秒数——设置过期时间，TTL key——查看还有几秒过期
7. set key value EX 秒数——在创建数据的时候设置过期时间。-1表示永久，-2表示不存在
8. persist key——将key设置为永久的。type key ——查看数据类型
9. del key——删除key，
10. redis-cli keys 'num*'  | xargs  redis-cli del ——用linux的管道符来删除。
11. flushall——清除所有数据。append key 数据——将数据加在key之前的数据后面。
12. incrby key 数字——加数字，decrby key 数字——减数字。



### 四、redis的列表

相当于栈，但分为左添加和右添加

基本命令：命令 key  value



1. lpush li 3 4 5 ——给li列表插入3 4 5 ，lrange li 0 2——显示从第0到第2个数据
2. 下标索引取值——LINDEX  key   下标值，LPOP  key——删除左边的一个。
3. lrem  key  2  value——从左边开始删除2个value。如果数字是0，则是把值全部删除

### 五、哈希数据

hash(散列类型)的键值也是一种字典结构，但字段值只能是字符串，不支持其他类型

- Hset  yige   name yige，hset  yige  age 18——在redis数据库中设置了yige:{name:‘yige’,age:'18'}
- Hget yige name——得到name字段值，
- hmset yige height 180 width 130——设置多个 
- hget yige——得到所有的字段
- hvals yige——得到所有的字段值
- hgetall yige——得到所有的字段以及字段值，hexists yige name——判断字段名是否存在
- hincrby yige height 2——对身高加2

### 六、集合

集合中的数据是无序的

- sadd se1 1 2 3 a b ——给se1添加1 2 3 a b，scard se1——统计数据的个数 
- spop se1 2——随机删除se1集合中的两个元素，sismember se1 1——判断1是否在集合se1中
- smembers se1——获取全部集合的元素，
- srandmember se1 3——当个数为正数时，随机获取3个不重复的数字，当为负数时，随机获取3个重复数字
- sinter se1 se2——算这两个的交集，sinterstore se3 se1 se2 ——将结果存储在se3中
- sunion se1 se2——并集，sdiff se1 se2——算这两个的差集.

有序集合关联了一个有效分数



### 七、有序集合

- zadd math 100  yige 90 liangge 80 ——设置分数值，zcard math——查看它对应的分数。
- zrange math  0 -1 ——默认权重从小到大排序，zrerange math 0 -1——取反排序
- zrem math wuge——删除wuge，zrangebyscore math 70 90 ——获取指定范围的数据
- 在90前加（意思是不包含的意思。zrangebyscore math 70 （90 limit  0 2 ——不包含90加分页展示，
- zcount math 80 100——在这个范围内统计个数，zincrby math 5 yige——给yige的权重加5分
- zremrangebyrank test 0 2——删除第1到第3个元素。
- 算交并集与无序集合相同，只是将s变为z的差别。

