**字符串对象的编码(encoding)可以是int,raw或者embstr其中之一**

如数字：

```
 set a 100 
 ok
 
```

redisObject对象属性值 ![](/assets/redis-对象-int.png)


如字符串:

```
set a baihui 
ok
```
 ![](/assets/redis-对象-raw.png)
 
 
> 其中type值不变，但是encoding和pro在变，变的只是底层数据结构不同，但是他们都属于字符串对象