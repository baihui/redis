
**redis对象的底层数据结构共有八种但是为了提高性能，一个对象的底层数据结构不止一种，而使用哪种数据结构 ，是由创建的redis对象中的encoding属性指定下面其中一种数据存放结构：**

   * REDIS_ENCODING_INT	long 类型的整数
   * REDIS_ENCODING_EMBSTR	embstr 编码的简单动态字符串
   * REDIS_ENCODING_RAW	简单动态字符串
   * REDIS_ENCODING_HT	字典
   * REDIS_ENCODING_LINKEDLIST	双端链表
   * REDIS_ENCODING_ZIPLIST	压缩列表
   * REDIS_ENCODING_INTSET	整数集合
   * REDIS_ENCODING_SKIPLIST	跳跃表和字典
   
   
   




