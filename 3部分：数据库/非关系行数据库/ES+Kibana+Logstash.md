# ES + Kibana + Logstash





[Elasticsearch 入门到精通](https://www.cnblogs.com/buchizicai/p/17093719.html)

kibana可以给我们提供一个elasticsearch的可视化界面，便于我们学习。



![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/1739260275493-91e9efb7-33f3-4a02-b933-081ede47eebf.png)

## 1. elasticsearch 介绍

2004年底层是基于**lucene**来实现的。 相比优势： 

- 支持水平扩张，支持分布式
- 提供Restful接口，可被任何语言调用



## 2. 倒排索引

倒排索引，是基于MySQL这样的正排索引而言的。

关键词： 文档， 词条

虽然要先查询倒排索引，再查询倒排索引，但是无论是词条、还是文档id都建立了索引，查询速度非常快！无需全表扫描。



![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/1739261302467-de128f1e-7be8-4720-84d9-db5e93fea1ee.png)



## 3. ES数据库基本概念

- 文档、字段
- 索引、映射
- MySQL 、 ES各自长处： 

- MySQL擅长事务类型操作，可以确保数据的安全和一致性
- ES：擅长海量数据的手说、分析、计算

- ![img](https://cdn.nlark.com/yuque/0/2025/png/2923566/1739261418095-6887420f-578f-4ea3-9987-6a69b45a07c1.png)



## 4. 安装es、kibana、分词器

分词器的作用是什么？

- 创建倒排索引时对文档分词
- 用户搜索时，对输入的内容分词

IK分词器有几种模式？

- ik_smart：智能切分，粗粒度
- ik_max_word：最细切分，细粒度

IK分词器如何拓展词条？如何停用词条？

- 利用config目录的IkAnalyzer.cfg.xml文件添加拓展词典和停用词典
- 在词典中添加拓展词条或者停用词条

- 扩张词词典：新的网络用语
- 停用词词典：宗教、政治敏感词 
- 增加词典后重启，配置生效



## 5. 索引库操作

索引库就类似数据库表，mapping映射就类似表的结构。

我们要向es中存储数据，必须先创建“库”和“表”。

### 5.1. Mapping映射属性

### 5.2. 索引库的CRUD

CRUD简单描述：

- 创建索引库：PUT /索引库名
- 查询索引库：GET /索引库名
- 删除索引库：DELETE /索引库名
- 修改索引库（添加字段）：PUT /索引库名/_mapping

**创建索引库和映射**： PUT

**查询索引库**： GET

**修改索引库**：这里的修改只能增加 新的字段到mapping 中， 

倒排索引结构虽然不复杂，但是一旦数据结构改变（比如改变了分词器），就需要重新创建倒排索引，这简直是灾难。因此索引库**一旦创建，无法修改mapping**。

虽然无法修改mapping中已有的字段，但是却**允许添加新的字段**到mapping中，因为不会对倒排索引产生影响。

语法如下

```plain
PUT /索引库名/_mapping
{
  "properties": {
    "新字段名":{
      "type": "integer"
    }
  }
}
```



**删除索引库： DELETE**







## 6. 文档操作

文档操作有哪些？

- 创建文档：POST /{索引库名}/_doc/文档id
- 查询文档：GET /{索引库名}/_doc/文档id
- 删除文档：DELETE /{索引库名}/_doc/文档id
- 修改文档：

- 全量修改：PUT /{索引库名}/_doc/文档id
- 增量修改：POST /{索引库名}/_update/文档id { "doc": {字段}}



## 7. RestAPI

ES官方提供了各种不同语言的客户端，用来操作ES。这些客户端的本质就是组装DSL语句，通过http请求发送给ES。官方文档地址：https://www.elastic.co/guide/en/elasticsearch/client/index.html





## 8. ES搜索引擎


elasticsearch的查询依然是基于JSON风格的DSL来实现的。

### 8.1. 1. DSL设置查询条件

Elasticsearch提供了基于JSON的DSL（[Domain Specific Language](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)）来定义查询。常见的查询类型包括：

- **查询所有**：查询出所有数据，*一般测试用*。例如：match_all
- **全文检索（full text）查询**：利用分词器对用户输入内容分词，然后去倒排索引库中匹配。例如：

- match_query
- multi_match_query

- **精确查询**：根据精确词条值查找数据，一般是查找keyword、数值、日期、boolean等类型字段。例如：

- ids
- range
- term

- **地理（geo）查询**：根据经纬度查询。例如：

- geo_distance
- geo_bounding_box

- **复合（compound）查询**：复合查询可以将上述各种查询条件组合起来，合并查询条件。例如：

- bool
- function_score

```plain
GET /indexName/_search
{
  "query": {
    "查询类型": {
      "查询条件": "条件值"
    }
  }
}

// 查询所有
GET /indexName/_search
{
  "query": {
    "match_all": {
    }
  }
}
```



## 9. 设置搜索结果

搜索的结果可以按照用户指定的方式去处理或展示。

#### 9.1.1. 2.0 搜索结果种类

查询的DSL是一个大的JSON对象，包含下列属性：

- query：查询条件
- from和size：分页条件
- sort：排序条件
- highlight：高亮条件
- aggs：定义聚合

![img](https://cdn.nlark.com/yuque/0/2025/png/2923566/1739262763571-5c900179-cd79-4a40-9343-8b53d4d28162.png)





## 10. RestClient查询文档

文档的查询同样适用昨天学习的 RestHighLevelClient对象，基本步骤包括：

- 1）准备Request对象
- 2）准备请求参数
- 3）发起请求
- 4）解析响应

#### 10.1.1. 快速入门

查询的基本步骤是：

1. 创建SearchRequest对象
2. 准备Request.source()，也就是DSL。

① QueryBuilders来构建查询条件

② 传入Request.source() 的 query() 方法

1. 发送请求，得到结果
2. 解析结果（参考JSON结果，从外到内，逐层解析）

发送查询请求：

![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/1739262975925-8732fd0f-5c0b-49ab-85b3-1c2f50f3442e.png)





## 11. ES与Mysql数据同步

elasticsearch中的酒店数据来自于mysql数据库，因此mysql数据发生改变时，elasticsearch也必须跟着改变，这个就是elasticsearch与mysql之间的**数据同步**。

### 11.1. 三种方法

常见的数据同步方案有三种：

- 同步调用
- 异步通知
- 监听binlog

方式一：同步调用

- 优点：实现简单，粗暴
- 缺点：业务耦合度高

方式二：异步通知【常用】

- 优点：低耦合，实现难度一般
- 缺点：依赖mq的可靠性

方式三：监听binlog

- 优点：完全解除服务间耦合
- 缺点：开启binlog增加数据库负担、实现复杂度高

#### 11.1.1. 同步调用

方案一：同步调用

![img](https://cdn.nlark.com/yuque/0/2025/png/2923566/1739263241005-9ba78fda-b5b9-4b04-8586-8b74fea176c6.png)

基本步骤如下：

- hotel-demo对外提供接口，用来修改elasticsearch中的数据
- 酒店管理服务在完成数据库操作后，直接调用hotel-demo提供的接口，

#### 11.1.2. 异步通知

方案二：异步通知

![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/1739263241016-9205b320-f7f1-4e40-a040-916a18af3c0f.png)

流程如下：

- hotel-admin对mysql数据库数据完成增、删、改后，发送MQ消息
- hotel-demo监听MQ，接收到消息后完成elasticsearch数据修改

#### 11.1.3. 监听binlog

方案三：监听binlog

![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/1739263240940-7b5bfc84-6a3e-4377-8c25-b527d8090f8c.png)

流程如下：

- 给mysql开启binlog功能
- mysql完成增、删、改操作都会记录在binlog中
- hotel-demo基于canal监听binlog变化，实时更新elasticsearch中的内容



## 12. ES集群

### 12.1. ES集群搭建

### 12.2. ES集群脑裂问题

选票机制（奇数）：

解决脑裂的方案是，要求选票超过 **( eligible节点数量 + 1 ）/ 2** 才能当选为主，因此eligible节点数量最好是奇数。对应配置项是discovery.zen.minimum_master_nodes，在es7.0以后，已经成为默认配置，因此一般不会发生脑裂问题

例如：3个节点形成的集群，选票必须超过 （3 + 1） / 2 ，也就是2票。node3得到node2和node3的选票，当选为主。node1只有自己1票，没有当选。集群中依然只有1个主节点，没有出现脑裂。



### 12.3. 集群分布式存储

### 12.4. 集群分布式查询

### 12.5. 集群故障转移

集群的master节点会监控集群中的节点状态，如果发现有节点宕机，会立即将宕机节点的分片数据迁移到其它节点，确保数据安全，这个叫做故障转移。