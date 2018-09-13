

**输入缓冲区：客户端状态的输入缓冲区用于保存客户端发送的命令请求**

    ``` C
    typedef struct redisClient { 
        // ... sds querybuf; // ... 
    } redisClient; 
    ```
    将命令保存在querybuf中，它的类型是sds字符串大小不能超过1G

