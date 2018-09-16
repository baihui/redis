##### 压缩列表（ziplist）是[列表对象](/dui-xiang/lie-biao-dui-xiang.md)和哈希对象的底层实现之一,当一个列表只包含少量列表项，并且每个列表项要么就是小整数值，要么就是长度比较短的字符串，那么就会使用压缩列表 



* **列表对象**
> quicklist结构是在redis 3.2版本中新加的数据结构，替换了zipList

如下

```

127.0.0.1:6379> rpush lis 123 456 bai
(integer) 3
127.0.0.1:6379> object encoding lis
"quicklist"

```

* **哈希对象**

 > hash对象包含少量的键值并且每个健和值必须是小整数和比较短字符串，使用压缩列表来做hash对象的底层结构。

```

127.0.0.1:6379> hmset has a a1 b b1
OK
127.0.0.1:6379> object encoding has
"ziplist"
127.0.0.1:6379>

```
