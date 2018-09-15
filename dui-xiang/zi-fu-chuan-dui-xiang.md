**字符串对象的编码(encoding)可以是int,raw或者embstr其中之一**


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
    
 * raw
   
   如果字符串对象保存的是字符串，并且长度**大于32**，那么就选用raw编码
   
 * embstr
    
    如果字符串对象保存的是字符串，并且长度**小于32**，那么就选用embstr
编码

 


