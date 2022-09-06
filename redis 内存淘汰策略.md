## redis 内存淘汰策略



```shell
# 内存淘汰算法
noeviction(默认策略)：对于写请求不再提供服务，直接返回错误（DEL请求和部分特殊请求除外）
allkeys-lru：从所有key中使用LRU算法进行淘汰
volatile-lru：从设置了过期时间的key中使用LRU算法进行淘汰
allkeys-random：从所有key中随机淘汰数据
volatile-random：从设置了过期时间的key中随机淘汰
volatile-ttl：在设置了过期时间的key中，根据key的过期时间进行淘汰，越早过期的越优先被淘汰


127.0.0.1:6379> config get maxmemory
# 获取当前的淘汰策略
127.0.0.1:6379> config get maxmemory-policy
```



#### LRU(Last Recently Used) 最近使用

redis 采用了一种近似 LRU 算法的处理，给每个 key 增加一个 24bit 的字段，记录当前 key 最近使用的时间，另外采用随机采样的方式，淘汰当前采样样本中符合条件的 key。

如果当前 key 被使用的次数很少，但是最近被使用过，会被表示为热点数据而不会被淘汰，此特性与 LFU 相反

1. Volatile-l ru 设置了过期时间的 key 中进行淘汰
2. Allkeys-lru 所有 key 中进行淘汰



#### LFU(Last Frequently Used) 最近最少频次使用

key 中记录当前 key 使用的次数，通过次数来判断是否为热点数据。

1. Volatile-lfu 设置了过期时间的 key 中进行淘汰
2. Allkeys-lfu 所有 key 中进行淘汰



#### TTL 过期时间

根据 key 的过期时间，淘汰即将过期的 key





