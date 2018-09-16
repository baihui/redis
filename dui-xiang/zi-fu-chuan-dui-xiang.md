**字符串对象的编码(encoding)可以是int,raw或者embstr其中之一 ，embstr和raw 底层都是[sdshdr字符串结构](/ji-he/zi-fu-chuan.md)**


* **encoding选择不同的编码也就是底层数据存放在不同结构体中**

 如数字：
 
 ```
  set a 100 
  ok
  
 ```

  ![](/assets/redis-对象-int.png)
  
  如字符串:
   
   ```
   set a "baihui 123456789abcdsdfefgasd 123456789abcdsdf" 
   ok
   ``` 
   ![](/assets/redis-对象-raw.png)
    
   > 其中type值不变，但是encoding和pro在变，变的只是底层数据结构不同，但是他们都属于字符串对象
   
   
   
* **int,raw和embstr 这三种编码如何选择**

 * int
    
     如果一个字符串对象需要存放的值是整数并且可以用long类型表示，那么encoding=int，并且将值，直接存放redisObject的ptr属性上，如果不是整数如有小数点那么用emstr编码表示。
     
     
     ```
     
     127.0.0.1:6379> set sds_int "123"
     OK
     127.0.0.1:6379> object encoding sds_int
     "int"
     
     127.0.0.1:6379> del sds_int
     (integer) 1
     127.0.0.1:6379> set sds_int 123.04
     OK 
     127.0.0.1:6379> object encoding sds_int
     "embstr"
    
     ```
   
      
 * **raw**
   
   如果字符串对象保存的是字符串，并且长度**大于32**，那么就选用raw编码
   
   ```
   
   127.0.0.1:6379> set sds_raw sdsadsadsadsdadasadsasdsadsda213212323132132321321aaaaaaa
   OK
   127.0.0.1:6379> object encoding sds_raw
   "raw"
   
   ```
   
 * **embstr**
    
    如果字符串对象保存的是字符串 或者long 和double，并且长度**小于32**，那么就选用embstr
    
```
     127.0.0.1:6379> set sds_int 123.04
     OK 
     127.0.0.1:6379> object encoding sds_int
     "embstr"
```


> embstr和raw编码的区别，两者都使用redisObject结构和sdshdr结构来表示字符串对象，但raw编码会调用两次内存分配函数来分别创建redisObject结构和sdshdr结构，而embstr编码则通过调用一次内存分配函数来分配一块连续的空间，空间中依次包含redisObject和sdshdr两个结构。所以embstr 释放空间和创建一次就可以了，而raw需要两次



* **编码转换**

   int编码的字符串对象和embstr编码的字符串对象在条件满足的情况下，会被转换为raw编码的字符串对象(只能)。
   
   
```
   
   
   
   127.0.0.1:6379> set int_rar 123
   OK
   127.0.0.1:6379> object encoding int_rar
   "int"
   127.0.0.1:6379> append int_rar "_raw"
   (integer) 7
   127.0.0.1:6379> object encoding int_rar
   "raw"
   
```
 


 


