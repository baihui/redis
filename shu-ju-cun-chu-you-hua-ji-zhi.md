#### zipmap优化hash:

前面谈到将一个对象存储在hash类型中会占用更少的内存，并且可以更方便的存取整个对象。省内存的原因是新建一个hash对象时开始是用zipmap来存储的。这个zipmap其实并不是hash table，但是zipmap相比正常的hash实现可以节省不少hash本身需要的一些元数据存储开销。尽管zipmap的添加，删除，查找都是O(n)，但是由于一般对象的field数量都不太多。所以使用zipmap也是很快的,也就是说添加删除平均还是O(1)。如果field或者value的大小超出一定限制后，redis会在内部自动将zipmap替换成正常的hash实现。这个限制可以在[配置文件](/redispei-zhi-wen-jian.md)中指定：


```

hash-max-zipmap-entries 512 #配置字段最多512个

hash-max-zipmap-value 64 #配置value最大为64字节

```


#### ziplist优化list:

_如果redisObject的type成员值是REDIS_LIST类型的，则当该list的元素个数小于配置值list-max-ziplist-entries且元素值字符串的长度小于配置值list-max-ziplist-value则可以编码成 REDIS_ENCODING_ZIPLIST 类型存储，否则采用 Dict 来存储(Dict实际是Hash Table的一种实现)，list采用ziplist数据结构存储数据，这样做一方面为了节省内存，另一方面这种结构式顺序存储的结构，能够更好利用cpu local和预取策略。_

配置如下所示：

```

list-max-ziplist-entries 512 #配置元素个数最多512个

list-max-ziplist-value 64 #配置value最大为64字节

```





#### intset优化set:

当set集合中的元素为整数且元素个数小于配置set-max-intset-entries值时，使用intset数据结构存储，否则转化为Dict结构，Dict实际是Hash Table的一种实现，key为元素值，value为NULL，这样即可在O(1)时间内判断集合中是否包含某个元素。

intset中有三种类型数组：int16_t类型、int32_t 类型、 int64_t 类型。至于怎么选择是那种类型的数组，是根据其保存的值的取值范围来决定的，初始化时是 int16_t，根据 set 中的最大值在[INT16_MIN, INT16_MAX] , [INT32_MIN, INT32_MAX], [INT64_MIN, INT64_MAX]的那个取值范围来动态确定整个数组的类型。例如set一开始是 int16_t 类型，当一个取值范围在 [INT32_MIN, INT32_MAX]的值加入到 set 时，则将保存 set 的数组升级成 int32_t 的数组。

intset元素限制的配置如下所示：

```
set-max-intset-entries 512 #配置元素个数最多512个

```


* ziplist优化sorted set:

根hash和list一样sorted set也有节约内存的方式，当sorted set的元素个数及元素大小小于一定限制时，它是用ziplist来存储。

这个限制的配置如下：

```

zset-max-ziplist-entries 128 #配置元素个数最多512个
zset-max-ziplist-value 64 #配置value最大为64字节

```




> Redis提供了很多关于优化内存的方法，上面这些配置的值都是默认配置，实际要根据我们具体的需求场景来调节，并要做大量的测试，以达到最优的效果。同时必须对Redis这些数据结构有很好的理解。