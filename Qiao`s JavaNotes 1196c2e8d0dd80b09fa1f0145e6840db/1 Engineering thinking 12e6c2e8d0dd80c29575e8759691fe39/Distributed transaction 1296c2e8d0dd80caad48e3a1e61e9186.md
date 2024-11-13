# Distributed transaction

### **Distributed transaction solution**

- Strong consistency：
    - 2PC protocol：add Coordinator. [the steps like rocketmq half transaction message]
    - Seata AT：
        - proxy intercepts the transaction SQL, records it in undo_log.
        - then binds it to the local transaction for rollback.
- Eventual consistency：base on MQ transaction messgae.