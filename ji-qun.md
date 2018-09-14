Redis 集群是分布式数据库的解决方案，通过分片来进行数据共享 
* 一个redis集群有多个节点组成 ,每个节点负责不同数据曹short 







> [配置文件](/redispei-zhi-wen-jian.md)中配置参数
![](/assets/屏幕快照 2018-09-14 下午4.26.46.png)

#cluster命令行


* ##### 集群(cluster)
    * CLUSTER INFO 打印集群的信息
    * CLUSTER NODES 列出集群当前已知的所有节点（node），以及这些节点的相关信息。 

* ##### 节点(node)
    * CLUSTER MEET <ip> <port> 将 ip 和 port 所指定的节点添加到集群当中，让它成为集群的一份子。
    * CLUSTER FORGET <node_id> 从集群中移除 node_id 指定的节点。
    * CLUSTER REPLICATE <node_id> 将当前节点设置为 node_id 指定的节点的从节点。
    * CLUSTER SAVECONFIG 将节点的配置文件保存到硬盘里面。 
    
* ##### 槽(slot)
    * CLUSTER ADDSLOTS <slot> [slot ...] 将一个或多个槽（slot）指派（assign）给当前节点。
    * CLUSTER DELSLOTS <slot> [slot ...] 移除一个或多个槽对当前节点的指派。
    * CLUSTER FLUSHSLOTS 移除指派给当前节点的所有槽，让当前节点变成一个没有指派任何槽的节点。
    * CLUSTER SETSLOT <slot> NODE <node_id> 将槽 slot 指派给 node_id 指定的节点，如果槽已经指派给另一个节点，那么先让另一个节点删除该槽>，然后再进行指派。
    * CLUSTER SETSLOT <slot> MIGRATING <node_id> 将本节点的槽 slot 迁移到 node_id 指定的节点中。
    * CLUSTER SETSLOT <slot> IMPORTING <node_id> 从 node_id 指定的节点中导入槽 slot 到本节点。
    * CLUSTER SETSLOT <slot> STABLE 取消对槽 slot 的导入（import）或者迁移（migrate）。 

* ##### 键 (key)
    * CLUSTER KEYSLOT <key> 计算键 key 应该被放置在哪个槽上。
    * CLUSTER COUNTKEYSINSLOT <slot> 返回槽 slot 目前包含的键值对数量。
    * CLUSTER GETKEYSINSLOT <slot> <count> 返回 count 个 slot 槽中的键。