# Redis —— Speed first

# Start with problem

1. The consistency issue between cache and database
    1. Base on CAP theory, there just one option between A or C.
    2. AP more make sense than CP cause Redis usually for improve high performance.
    3. AP solution: Update database first, then delete cache by MQ for compatible to retry
2. Cache penetration： key doesn't exist in database
    1. Bloom filter: quickly determine whether an element exists in the collection, allowing misjudgments
    2. Inteface current limiting, add IP blacklist when attacked
3. Cache breakdown or avalanche：usually in some activity or big activity
    1. Preheat in advance by manual control
    2. Add lock when generate cache
    3. Set random time expiration.
4. Large key
    1. Temp：delete useless data , split large key
    2. Business analysis , consider optimize or design new solution.
5. Hot key
    1. Add JVM cache split the pressure
    2. Expand slave nodes

# Ref

- Official website: https://redis.io/
- Hard to find doc from official website：https://redis.io/learn/howtos/quick-start
- Official Group：https://discord.com/invite/redis
- Github Discussion：https://github.com/redis/redis/discussions
- Wikipedia: https://en.wikipedia.org/wiki/Redis

# History

- Redis full name : Remote Dictionary Sever, created by [antirez](https://github.com/antirez) for developing a real-time web log analyer in 2009.
    - Description about this guy: Computer programmer based in Sicily, Italy. I mostly write OSS software. Born 1977. Not a puritan.
- Sponsored by [Redis Labs](https://en.wikipedia.org/wiki/Redis_(company)) in 2015.
- In 2018, Redis Labs announced: some modules use SSPL, core modules retain BSD.
- In 2020, Antirez quits his job maintaining Redis，his blog：http://antirez.com/news/133
- In 2024.03, Redis Labs announced that it would abandon the BSD protocol and switch to RSAL and SSPL protocols, increasing commercial restrictions on cloud vendors, which immediately sparked heated discussions in the developer community. https://www.51cto.com/article/671655.html

# Install

- snap install redisinsight
- snap install redis
- init redis config:  [https://redis.io/chat?page=1&q=after+installing+redis+by+snap%2C+how+can+I+config+it+so+that+redis+insight+could+connect+it+%3F](https://redis.io/chat?page=1&q=after+installing+redis+by+snap%2C+how+can+I+config+it+so+that+redis+insight+could+connect+it+%3F)

# Benchmark about search

- https://redis.io/blog/search-benchmarking-redisearch-vs-elasticsearch/
- https://myscale.com/blog/redis-vs-elasticsearch-search-benchmarking-showdown/
- https://developer.aliyun.com/article/1552219

RedisSearch：light but fast

ElasticSearch：heavy but poweful

# Overall design mind

**Speed first**

- Base on Memory Calculation
- Single thread and IO Mitiplexing Optimization(Single thread watch multi connection)
- Data structure like jump table by index

# Data structure

- string
- hash（actually, is map， just call it hashmap）
    - bitmap： similar with hashmap but value limit in bit , only save 0 or 1 so that saving space.
    - bitfield：support bit operation but hard to use and understand or maintain
- List，Set, Sorted Set
    - geospatial： for nearby situation, search radius for specific place
- Streams（supported in v5.0）light mq than kafka

# Transaction

https://redis.io/docs/latest/develop/interact/transactions/

ACID

- Atomicity，Consistency, Isolation
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