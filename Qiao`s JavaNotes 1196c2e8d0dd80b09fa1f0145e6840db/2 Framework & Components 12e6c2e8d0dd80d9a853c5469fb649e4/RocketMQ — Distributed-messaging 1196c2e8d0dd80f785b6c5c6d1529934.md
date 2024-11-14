# RocketMQ — Distributed-messaging

# Ref

- Official website: https://rocketmq.apache.org/
- Github discussion: https://github.com/apache/rocketmq/discussions

# Message type and implemention strategy

- Normal message：Default
- Ordered message
    - **Producer**：single thread send msg
    - **Broker**：save msg by single queue：MessageQueueSelector
    - **Consumer**：single thread consume msg
- Delay message
    - Send to Delay Queue(SCHEDULE_TOPIC_XXX) first
    - scan by task , then send to target topic when expired
    - Version diff: V4.x[set stable delay level] V5[support custom delay time]
- **Transaction message**
    - Definition: Focus the consistency between producer local transaction and send msg , not solve global transaction problem.
    - Steps：
        - Producer send half transaction msg to broker
        - Broker notify sucess of half transaction msg
        - Producer execute local transaction
        - Producer notify broker send msg or rollback and delete msg

# Current limiting strategy

- RocketMQ support basic limiting logic and some config like : `consumeThreadMax`
- Use sentinel to do fine control (Recommand)

# Repeted consumption solution

- Idempotent design based on business
- Deduplication design based on msg

# Experience on MQ

- https://blog.csdn.net/huoyunshen88/article/details/42776961
- https://www.cnblogs.com/fulongyuanjushi/p/16457753.html