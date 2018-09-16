#### Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部或者尾部,列表对象的编码(encoding)可以是ziplist或者linkedlist 



如下将值添加到列表bh中：

```
127.0.0.1:6379> rpush k1 1 a b c 2
(integer) 5
    

127.0.0.1:6379> rpush k1 2
(integer) 6

127.0.0.1:6379> LRANGE k1 0 -1
1) "1"
2) "a"
3) "b"
4) "c"
5) "2"
6) "2"


```


