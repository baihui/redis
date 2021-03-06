##### 主从同步是基于[rdb文件](/chi-jiu-hua.md)的，如果开启了主从同步，即使你不开启rdb，也会默认自动生成的，不然无法进行数据同步

```
SLAVEOF < master_ ip> < master_ port>
```


* **连接**

* **通过PSYNC命令进行同步**
PSYNC命令具有完整重同步（fullresynchronization）和部分重同步（partialresynchr    onization）两种模式：
    
    * 其中完整重同步用于处理初次复制情况：完整重同步的执行步    骤和SYNC命令的执行步骤基本一样，它们都是通过让主服务器创建并发送RDB文件，以    及向从服务器发送保存在缓冲区里面的写命令来进行同步。
    
    * 而部分重同步则用于处理断线后重复制情况：当从服务器在断线后重新连接主服务器时，如果条件允许，主服务器可以将主从服务器连接断开期间执行的写命令发送给从服务器，从服务器只要接收并    执行这些写命令，就可以将数据库更新至主服务器当前所处的状态部分重同步功能由以下三个部分构成：
    
        * **主服务器的复制偏移量和从服务器的复制偏移量**。
        
            ![](/assets/屏幕快照 2018-09-09 下午6.37.21.png)
            通过对比主从服务器的复制偏移量，程序可以很容易地知道主从服务器是否处于一致状态
        * **主服务器的复制积压缓冲区**
            
            复制积压缓冲区里面会保存着一部分最近传播的写命令，并且复制积压缓冲 区会为队列中的每个字节记录相应的复制偏移量 来确认全部同步还是部分同步(如果offset偏移量之后的数据**（也即是偏移量offset+1开始的数据）仍然存在于复制积压缓冲区里面，那么主服务器将对从服务器执行部分重同步操作,否者完全同步) **         
           ![](/assets/redis.png)
           
           
               > 复制积压缓冲区是master服务器维护的固定长度，先进先出的队列（FIFO）默认大小1M
               [通过修改redis.conf中](/redispei-zhi-wen-jian.md) repl-backlog-size **设置大小**
               Repl-backlog-size 公式计算 （slave（从服务器掉线平均秒）*master-writer（主服务器每秒的写入量））
               
        * **服务器的运行ID（runID）**
        
        当从服务器对主服务器进行初次复制时，主服务器会将自己的运行ID传送给从服务器，而从服务器则会将这个运行ID保存起来。主服务根据从服务传递的ID判断是否自己，如果不是自己的ID将会执行完整重同步。
        
         
    
* **命令传播  **

    当完成了同步之后,主从服务器就会进入命令传播阶段,这时主服务器只要一直将自己执行的写命令发送给从服务器，而从服务器只要一直接收并执行主服务器发来的写命令，就可以保证主从服务器一直保持一致了


* **心跳检测**

    在命令传播阶段,从服务器默认会以每秒一次的频率,向主服务器发送命令：
     
 ```
 
 REPLCONF ACK <replication_offset>
 
 ```
 
$$ 其中replication_offset是从服务器当前的复制偏移量,发送REPLCONF ACK命令对于主从服务器有三个作用 $$：
    * 检测主从服务器的网络连接状态 
        通过向主服务器发送 INFO replication命令， 在列出的从服务器列表的lag一栏表示多长时间没通讯了，
 
        ```
        info replication 
        ```
        
        lag的值应该在0秒或者1秒之间跳动，如果超过1秒的话，说明主从服务器之间的连接出现了故障。
 
    * 辅助实现min-slaves选项
    
    * 检测命令丢失
    
  
#### redis主从复制常见的一些问题


1.数据复制的延迟

读写分离时，master会异步的将数据复制到slave，如果这是slave发生阻塞，则会延迟master数据的写命令，造成数据不一致的情况

解决方法：可以对slave的偏移量值进行监控，如果发现某台slave的偏移量有问题，则将数据读取操作切换到master，但本身这个监控开销比较高，所以关于这个问题，大部分的情况是可以直接使用而不去考虑的。

2.读到过期的数据

我们知道redis在删除过期key的时候，是有两种策略，第一种是懒惰型策略，即只有当redis操作这个key的时候，发现这个key过期，就会把这个key删除。第二种是定期采样一些key进行删除。

针对上面说的两种过期策略，会有个问题，即如果我们过期key的数量非常多，而采样速度根本比不上过期key的生成速度时会造成很多过期数据没有删除，但在redis里master和slave达成一种协议，slave是不能处理数据的（即不能删除数据）而我们的客户端没有及时读到到过期数据同步给master将key删除，就会导致slave读到过期的数据（这个问题已经在redis3.2版本中解决）

主从配置不一致

这个问题一般很少见，但如果有，就会发生很多诡异的问题

例如：

1. maxmemory配置不一致：这个会导致数据的丢失

原因：例如master配置4G，slave配置2G，这个时候主从复制可以成功，但，如果在进行某一次全量复制的时候，slave拿到master的RDB加载数据时发现自身的2G内存不够用，这时就会触发slave的maxmemory策略，将数据进行淘汰。更可怕的是，在高可用的集群环境下，如果我们将这台slave升级成master的时候，就会发现数据已经丢失了。

##### 数据结构优化参数不一致（例如hash-max-ziplist-entries）：

这个就会导致内存不一致原因：例如在master上对这个参数进行了优化，而在slave没有配置，就会造成主从节点内存不一致的诡异问题。

##### 规避全量复制

首先，我们知道，redis复制有全量复制和部分复制两种（这个我前面博客有写到）而全量复制的开销是很大的。那么我们来看看，如何尽量去规避全量复制。

 第一次全量复制

当我们某一台slave第一次去挂到master上时，是不可避免要进行一次全量复制的，那么，我们如何去想办法降低开销呢？

方案1：小主节点，例如我们把redis分成2G一个节点，这样一来，会加速RDB的生成和同步，同时还可以降低我们fork子进程的开销（master会fork一个子进程来生成同步需要的RDB文件，而fork是要拷贝内存快的，如果主节点内存太大，fork的开销就大）。

方案2：既然第一次不可以避免，那我们可以选在集群低峰的时间（凌晨）进行slave的挂载。

2.节点RunID不匹配

例如我们主节点重启（RunID发生变化），对于slave来说，它会保存之前master节点的RunID，如果它发现了此时master的RunID发生变化，那它会认为这是master过来的数据可能是不安全的，就会采取一次全量复制

解决办法：对于这类问题，我们只有是做一些故障转移的手段，例如master发生故障宕掉，我们选举一台slave晋升为master（哨兵或集群）

##### 复制积压缓冲区不足

我在全量复制与部分复制那篇文章提到过，master生成RDB同步到slave，slave加载RDB这段时间里，master的所有写命令都会保存到一个复制缓冲队列里（如果主从直接网络抖动，进行部分复制也是走这个逻辑），待slave加载完RDB后，拿offset的值到这个队列里判断，如果在这个队列中，则把这个队列从offset到末尾全部同步过来，这个队列的默认值为1M。而如果发现offset不在这个队列，就会产生全量复制。

解决办法：增大复制缓冲区的配置 rel_backlog_size 默认1M，我们可以设置大一些，从而来加大我们offset的命中率。这个值，我们可以假设，一般我们网络故障时间一般是分钟级别，那我们可以根据我们当前的QPS来算一下每分钟可以写入多少字节，再乘以我们可能发生故障的分钟就可以得到我们这个理想的值。

##### 规避复制风暴

什么是复制风暴？举例：我们master重启，其master下的所有slave检测到RunID发生变化，导致所有从节点向主节点做全量复制。尽管redis对这个问题做了优化，即只生成一份RDB文件，但需要多次传输，仍然开销很大。

1.单主节点复制风暴：主节点重启，多从节点全量复制
    
    解决：更换复制拓扑如下图：
![](/assets/175610_poHK_3371837.png)
    
    
    
    1.我们将原来master与slave中间加一个或多个slave，再在slave上加若干个slave，这样可以分担所有slave对master复制的压力。（这种架构还是有问题：读写分离的时候，slave1也发生了故障，怎么去处理？）
    
    2.如果只是实现高可用，而不做读写分离，那当master宕机，直接晋升一台slave即可。
    
_2.单机器复制风暴：机器宕机后的大量全量复制，如下图_：    

 ![](/assets/180134_ChpF_3371837.png)
          
> 当machine-A这个机器宕机重启，会导致该机器所有master下的所有slave同时产生复制 
解决：
* 主节点分散多机器（将master分散到不同机器上部署）
* 还有我们可以采用高可用手段（slave晋升master）就不会有类似问题了。     







 




                            