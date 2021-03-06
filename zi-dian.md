
##### 字典又称为map是一种用于保存键值对的抽象数据结构，对K和V计算都是通过type指向dictType结构体提供，以及k-v数据存放在ht指向的哈希表节点数组中 如图：
 
![](/assets/dict123.png)

 * **字典结构**： 

    ``` 
    typedef struct dict 
    { 
        //类型特定函数
        dictType *type; 
        //私有数据 
        void *privdata; 
        //哈希表
        dictht ht[ 2]; 
        //rehash索引当rehash不在进行时， 值为-1
        in trehashidx;
    } dict;
    
    ```
    * type属性是一个指向dictType结构的指针，每个dictType结构保存了一簇用于操作特定类型键值对的函数，用途不同的字典设置不同的类型特定函数。
    
    ``` 
    typedef struct dictType { 
        //计算哈希值的函数 
        unsigned int (*hashFunction)( const void *key); 
        //复制键的函数 
        void *(*keyDup)( void *privdata, const void *key); 
        //复制值的函数
        void *(*valDup)( void *privdata, const void *obj); 
        // 对比键的函数
        int (*keyCompare)( void *privdata, const void *key1, const void *key2); 
        // 销毁键的函数 
        void (*keyDestructor)( void *privdata, void *key); 
        //销毁值的函数 void (*valDestructor)( void *privdata, void *obj); 
    } dictType;
    
    ``` 
    > 如新的键值对添加到字典时，先调用type指向dictType结构体中hashFunction函数来计算键的哈希值，然后再根据hash值计算索引值，最后封装成dictEntry放到哈希表数组的指定索引上 如下：
    ```
    hash = dict-> type-> hashFunction( k0);//hash值
    index = hash& dict-> ht[0]. sizemask = 8 & 3 = 0; //索引值
    ```

    * privdata属性则保存了需要传给那些类型特定函数的可选参数。
    * ht属性是一个包含两个项的数组，数组中的每个项都是一个dictht哈希表，一般情况下，字典只使用ht[0]哈希表，ht[1]哈希表只会在对ht[0]哈希表进行rehash时使用。除了ht[1]之外，
    * rehashidx 是一个和rehash有关的属性，它记录了rehash目前的进度，如果目前没有在进行rehash，那么它的值为-1。
     
 
 
 





 





* **哈希表结构体**

    ``` 
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

    * table属性是一个数组，数组中的每个元素都是一个指向dict.h/dictEntry结构的指针，每个dictEntry结构保存着一个键值对。
    * size属性记录了哈希表的大小，也即是table数组的大小，
    * used属性则记录了哈希表目前已有节点（键值对）的数量 
    * sizemask属性的值总是等于size-1，这个属性和哈希值一起决定一个键应该被放到table数组的哪个索引上面
    
    
    
* **节点结构体**

``` 

typedef struct dictEntry { 
//键 
void *key; 
//值 
union{ void *val; uint64_ tu64; int64_ ts64; } v; 
//指向下个哈希表节点， 形成链表 
struct dictEntry *next;
} dictEntry ;

```

    * key属性保存着键值对中的键，
    * v属性则保存着键值对中的值，其中键值对的值可以是一个指针，或者是一个uint64_t整数，又或者是一个int64_t整数。
    * next属性是指向另一个哈希表节点的指针，这个指针可以将多个哈希值相同的键值对连接在一次，以此来解决键冲突（collision）的问题。





