 **每个key和value都是使用对象表示的,Redis共有五种对象的类型分别是:**

> 
* REDIS_STRING	字符串对象
* REDIS_LIST	列表对象
* REDIS_HASH	哈希对象
* REDIS_SET	集合对象
* REDIS_ZSET	有序集合对象


Redis中的一个对象的结构体表示如下：

```
/*
 * Redis 对象
 */
typedef struct redisObject {
 
    // 类型
    unsigned type:4;        
 
    // 不使用(对齐位)
    unsigned notused:2;
 
    // 编码方式
    unsigned encoding:4;
 
    // LRU 时间（相对于 server.lruclock）
    unsigned lru:22;
 
    // 引用计数
    int refcount;
 
    // 指向对象的值
    void *ptr;
 
} robj;

```


> type表示了该对象的对象类型，即上面五个中的一个。但为了提高存储效率与程序执行效率，每种对象的底层数据结构实现都可能不止一种。encoding就表示了对象底层所使用的编码。下面先介绍每种底层数据结构的实现，再介绍每种对象类型都用了什么底层结构并分析他们之间的关系。

* Redis对象底层数据结构底层数据结构共有八种，如下表所示：

   * REDIS_ENCODING_INT	long 类型的整数
   * REDIS_ENCODING_EMBSTR	embstr 编码的简单动态字符串
   * REDIS_ENCODING_RAW	简单动态字符串
   * REDIS_ENCODING_HT	字典
   * REDIS_ENCODING_LINKEDLIST	双端链表
   * REDIS_ENCODING_ZIPLIST	压缩列表
   * REDIS_ENCODING_INTSET	整数集合
   * REDIS_ENCODING_SKIPLIST	跳跃表和字典
   
   
   


