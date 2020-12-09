## cassandra介绍优点、缺点、设计及运维总结

<u>cassandra</u> 是一套开源的分布式NoSQL数据库系统， 
是一堆数据库节点共同构成的一个分布式网络服务，对cassandra 的一个写操作，会被复制到其他节点，读操作也会被路由到某个节点上去，对于cassandra 集群来说，扩展非常简单， 在集群中添加节点即可。 

### 三大优点：

* *格式的灵活性*，像文档存储在系统运行时随意添加或移除字段。

* 真正的高可扩展性， 纯粹意义的水平扩展、线性可扩展、提高吞吐量、增加集群节点数量，因此可以保证快速的响应。
* 多数据中心识别 ， 避免一个数据中心起火，一个备用的数据中心至少有每一条数据的完全复制。

### cassandra 支持事务

* 设置键可以范围查询

* 列表数据结构，可将超级列添加到5五维，用以索引 ，

* 分布式写操作

* 基于column 的结构化数据

### cassandra 核心组件 

- GossIp

- ParTlTloner : 负责在集群中 分配数据， 使用Hash作为主键 

- 复制策略：  确定哪个节点放置数据，以及复制的份数，

- cassandra.yaml：主配置文件，设置集群的初始化配置、表的缓存参数、调优参数和资源使用、超时设定、客户端连接、备份和安全。

### cassandra 数据库设计和维护总结

1. 分区字段设计时选用 timeuuid 字段， 比如每日零点 或者 每小时的时间戳 

2. 索引字段根据需求设计

3. 使用索引时的查询条件，务必带上分区字段的查询条件，否则会去所有分区的索引中查询，效率低下，查询超出10w个墓碑，会抛出异常。

4. 高基字段不上索引， 比如男女、true /false 

5. 由于cassandra 的read repair 机制， 执行大量删除操作后出现read timeout 需要在每台cassandra 的bin 目录下进行 

   ``` shell
   ./nodetool flush 
   ./nodetool $keyspace $table 强制合并sstable 
   ```

6. 如果cassandra 出现不同数据节点一致性错误，需要执行

   ``` shell
   ./nodetool repair $keyspace $table 
   ```

7. 如果repair 还不能解决问题需要执行   <u>./sstablescrub $ kyespace $table</u>  清洗掉损坏的数据，（注意，此操作中断容易造成数据损坏，最好执行前做好快照）

8. cassandra 默认开启啦安全模式，执行drop、truncate 等敏感字段会对数据做一次快照，过多快照会导致cassandra 启动时遍历目录环节话费过多时间，最终可能导致几个小时才能启动，
   这个时候需要执行   ./nodetool cleansnapshot $keyspace



cassandra 基础使用可参考： https://blog.csdn.net/doc_sgl/article/details/52888574

​			   

