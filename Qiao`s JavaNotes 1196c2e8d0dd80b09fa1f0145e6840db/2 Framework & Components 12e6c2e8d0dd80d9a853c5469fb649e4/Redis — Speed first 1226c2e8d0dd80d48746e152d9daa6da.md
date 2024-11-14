# Redis — Speed first

# Ref

- Official website: https://redis.io/
- Hard to find doc from official website：https://redis.io/learn/howtos/quick-start
- Official Group：https://discord.com/invite/redis
- Github Discussion：https://github.com/redis/redis/discussions
- Wikipedia: https://en.wikipedia.org/wiki/Redis

# Big things

- Redis full name : Remote Dictionary Sever, created by [antirez](https://github.com/antirez) for developing a real-time web log analyer in 2009.
    - Description about this guy: Computer programmer based in Sicily, Italy. I mostly write OSS software. Born 1977. Not a puritan.
- Sponsored by [Redis Labs](https://en.wikipedia.org/wiki/Redis_(company)) in 2015.
- In 2018, Redis Labs announced: some modules use SSPL, core modules retain BSD.
- In 2020, Antirez quits his job maintaining Redis，his blog：http://antirez.com/news/133
- In 2024.03, Redis Labs announced that it would abandon the BSD protocol and switch to RSAL and SSPL protocols, increasing commercial restrictions on cloud vendors, which immediately sparked heated discussions in the developer community.

The news about protocol from BSD to SSPL：

- https://www.infoq.cn/article/ee6dyubdjdvsr369zune
- https://www.infoq.cn/article/cyh0hqiNW99Eh61mEaY3
- https://www.businesswirechina.com/zh/news/57686.html
- https://pigsty.io/zh/blog/db/redis-oss/
- https://blog.csdn.net/baidu_41642080/article/details/136913195?ops_request_misc=%257B%2522request%255Fid%2522%253A%252223D5A2F0-A6C8-45FC-8536-6F63354768BC%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=23D5A2F0-A6C8-45FC-8536-6F63354768BC&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-5-136913195-null-null.142%5Ev100%5Epc_search_result_base9&utm_term=redis%E5%BC%80%E6%BA%90&spm=1018.2226.3001.4187
- https://36kr.com/p/2700542716409992
- https://help.aliyun.com/zh/redis/?spm=5176.28508143.J_XmGx2FZCDAeIy2ZCWL7sW.48.20f8154acrU51w&scm=20140722.S_help@@%E6%96%87%E6%A1%A3@@26340.S_RQW@ag0+BB2@ag0+BB1@ag0+hot+os0.ID_26340-RL_redis-LOC_suggest~UND~product~UND~doc-OR_ser-PAR1_213e394617287210433126713e1726-V_3-P0_0
- https://cloud.tencent.com/product/crs
- https://aws.amazon.com/cn/elasticache/redis/
- https://www.infoq.cn/article/ppo8vzpth59mqj8np0hn
- https://www.huaweicloud.com/zhishi/redis-ky.html
- https://fanjingbo.com/post/tan-tan-redisxiu-gai-licenseshi-jian
- https://huangz.blog/2024/post-redis-era.html

# Install in linux

```jsx
snap install redisinsight
snap install redis
```

Init redis config:  [https://redis.io/chat?page=1&q=after+installing+redis+by+snap%2C+how+can+I+config+it+so+that+redis+insight+could+connect+it+%3F](https://redis.io/chat?page=1&q=after+installing+redis+by+snap%2C+how+can+I+config+it+so+that+redis+insight+could+connect+it+%3F)

# Benchmark about search

- RedisSearch：light but fast
- ElasticSearch：heavy but poweful

Ref：

- https://redis.io/blog/search-benchmarking-redisearch-vs-elasticsearch/
- https://myscale.com/blog/redis-vs-elasticsearch-search-benchmarking-showdown/
- https://developer.aliyun.com/article/1552219

# Overall design mind

**Speed first**

- Base on Memory Calculation
- Single thread and IO Mitiplexing Optimization(Single thread watch multi connection)
- Data structure like jump table implemented by index

# Data structure

- string
- hash（actually, is map， just call it hashmap）
    - bitmap： similar with hashmap but value limit in bit , only save 0 or 1 so that saving space.
    - bitfield：support bit operation but hard to use and understand or maintain
- List 、Set、 Sorted Set
    - geospatial： for nearby situation, search radius for specific place
- Streams（supported in v5.0）light mq than kafka

# Transaction

https://redis.io/docs/latest/develop/interact/transactions/

ACID about Redis

- Atomicity，Consistency， Isolation
    - single thread，natually friendly for those
    - single command： totally support Aomicity, Consitency,Isolation
    - multi commands：
        - supoort 4 commands（Multi, Exec, Watch, Discard）
        - no rollback，part of atomicity
        - can not keep consistency
        - can not keep Isolation
- Durability：RDB（snapshot backup）, AOF（record write behavior， fsync： on writing or schedule）

# Distributed Locks

- set command and nx param，add expired time
- Lock renew：solve operation can not be finished before expiration time
    - Redission support watch log thread to polling and renew

# Redis Deploy Strategy

- Single machine: original version
- Master-slave: high availability, support manual failover
- Sentinel: automatic failover
- Cluster: add sharding and expansion features

Ref：

- https://juejin.cn/post/6844904191236767751#heading-5
- https://www.cnblogs.com/phpphp/p/18264191-
- https://blog.csdn.net/weixin_45433817/article/details/136994475

# Monitor

https://grafana.com/grafana/plugins/redis-datasource/

# Q&A

## 1. The **consistency** issue between **cache and database**

Actually，it`s CAP problem.

- Already use P when under the background of cache and database.
- AP system solution[Eventual consisitency]：Process database first, then process cache by MQ for compatible to retry.
- CP system solution[Strong consistency]： not recommended to use redis to implement strong consistency logic

## 2. Cache penetration： key doesn't exist in database

- Bloom filter: quickly determine whether an element exists in the collection, allowing misjudgments.
- Inteface current limiting, add IP blacklist when attacked.

## 3. Cache breakdown or avalanche：usually in some activity or big activity

1. Preheat in advance by manual control
2. Add lock when generate cache
3. Set random time expiration.

## 4. Large key

- Temp：delete useless data , split large key
- Business analysis , consider optimize or design new solution.

## 5. Hot key

- Add JVM cache split the pressure
- Expand slave nodes