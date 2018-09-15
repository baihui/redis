**redis使用redisObject结构体来表示一个对象，一个k-v键值对的创建会包含两个对象，一个对象键对象，另一个值对象，健对象总是字符串，而值对象却有五种类型的其中一种，这五种对象底层的[数据结构](/shu-ju-jie-gou.md)是动态引用的**

 
redis中的一个对象的结构体表示如下：

```
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


* type表示了该对象的对象类型，即下面五个中的一个 

* encoding就表示了对象底层所使用的编码 

![](/assets/redis-对象.png)

* [字符串对象](/sdsdong-tai-zi-fu-4e3229.md)

* [列表对象](/dui-xiang/lie-biao-dui-xiang.md)

* [哈希对象](/dui-xiang/ha-xi-dui-xiang.md) 

* [集合对象](/dui-xiang/ji-he-dui-xiang.md)
   
    * [字典结构](/zi-dian.md)是一种抽象结构，实际数据存放在哈希表中，而他复制对K的哈希值函数和指向哈希表的指针
         
    * 整数集结构
    

* [有序集合对象](/dui-xiang/you-xu-ji-he-dui-xiang.md)












