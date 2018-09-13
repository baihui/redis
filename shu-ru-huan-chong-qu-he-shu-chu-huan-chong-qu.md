

#### 输入缓冲区

**客户端状态的输入缓冲区用于保存客户端发送的命令请求**

    ``` C
    
    typedef struct redisClient { 
        ... 
        sds querybuf ; 
        ... 
    } redisClient; 
    
    ```

> 命令保存在querybuf中，它的类型是[sds字符串](/sdsdong-tai-zi-fu-4e3229.md)输入缓冲区的大小会根据输入内容动态地缩小或者扩大，但它的最大大小不能超过1GB，否则服务器将关闭这个客户端 













#### 输出缓冲区