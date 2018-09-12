

* 字典又称为符号表、 关联数组 或 映射（ map）， 是一 种用于保存键值对的抽象数据结构。 在字典中一个键（ key） 可以和 一个值（ value） 进行关联（ 或者说 将 键 映射 为 值）， 这些关联的键和值就称为键值对。


> Redis的字典使用哈希表作为底层实现，一个哈希表里面可以有多个哈希表节点，而每个哈希表节点就保存了字典中的一个键值对


哈希表结构体

``` C
typedef struct dictht { 
//哈希表数组
dictEntry **table; 
//哈希表大小 
unsigned long size; 
//哈希表大小掩码，用于计算索引总是等于size-1 
unsigned long sizemask; 
//该哈希表已有节点的数量
unsigned long used;
} dictht;

```


