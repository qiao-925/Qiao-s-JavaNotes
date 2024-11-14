# MySQL

https://www.sjkjc.com/mysql-ref/json_search/

https://www.sjkjc.com/postgresql-ref/json_array_elements/

https://www.sjkjc.com/postgresql/json-type/

```markdown
**1. 范式和反范式设计**

数据冗余的取舍
范式：最小冗余
反范式： 在一些业务场景下，可能需要冗余一部分的数据来满足当前的需求

**2. 三大范式是什么？**

- 每一列都不能再分（不能有逗号分隔的值）
- 每一列都必须依赖表的主键
- 每一列都不能传递依赖其他非主键的列（拆两个表）

**3. MySQL和PostgreSQL的区别？**

**数据量级：** MySQL通常通常用于处理中小规模数据，PostgreSQL可以用来处理大规模数据
**写入性能:** PostgreSQL优于MySQL
**复杂数据类型：** 如对JSON的操作语法支持更加强大，对比mysql

**4. MyISAM和 InnoDB的区别？**
InnoDB支持事务，行锁
MyISAM不支持事务，表锁

**5. 什么是事务？**
  一组数据库操作，要么全部执行，要么全部回滚

**6. 事务的ACID属性？**

  - 原子性：事务是一个不能再被分割的单位
  - 一致性：确保事务执行前后数据库处在合法的状态，满足数据库定义的规则；否则就会回滚事务。
  - 隔离性：事务之间的操作是互相隔离的，这个是通过MySQL的多版本并发控制实现的
  - 持久性：事务成功提交后会在数据库中持久保存

**7. Mysql MVCC原理**

全名：Muliti-Version-Concurrency Control 多版本并发控制
作用：允许多个事务同时访问数据库，同时保持事务的隔离，不会互相影响
原理：

1. 系统会给每行数据分配版本号，然后事务开始的时候也会有一个版本号
2. 事务对数据进行查询时，根据版本号的比较，事务只能读取大于或者等于当前事务版本号的数据
3. 事务对数据进行修改时，会创建数据的副本，分配一个新的版本号，原始数据保持不变，直到事务提交。
4. 回滚，提交：如果事务发生回滚，所有修改都被丢弃，如果事务提交，其他事务就会对它可见。

**8. 事务的操作命令？**

  - START TRANSACTION
  - COMMIT
  - ROOLBACK
  - SAVEPOINT
  - SET AUTOCOMIT

**9. 事务的隔离级别？**

数据库管理事务之间的可见性和操作。

**10. 四个隔离级别？**

- 读未提交：最低的隔离级别，允许事务可以读取另一个事务没有提交的数据，可能导致脏读，不可重复读，幻读
- 读已提交：mysql默认的隔离级别，事务只能读已提交的数据，解决了脏读问题，但会带来不可重复读，幻读问题
           不可重复读：对同一个数据的重复读取，由于其他数据进行了修改，得到结果不一样的数据
           幻读：对同一个范围的数据重复读取，
- 可重复读：一个事务读取相同数据时，会得到一致的结果。解决了不可重复读和部分幻读问题。
- 串行化：消除事务的并发问题，但是性能会受限制

**11. 聚簇索引与非聚簇索引区别？**

聚簇索引：数据和索引是放在一块的
非聚簇索引：分开放，类似书的目录和具体的内容

MySQL使用的是混合索引的方式，主键使用聚簇索引，非主键列使用非聚簇索引

**12. MySQL索引失效情况？**

- like查询%放前面
- where后面使用函数
- 使用or条件

**13. B树和B+树区别？**

- 数据分布：B树所有的节点都包含数据，B+树只有叶子节点包含数据
- 查询性能：B树对单个节点的查询效率高，B+树对范围查询效率高
- 写入操作：B+树只需要操作叶子结点，B树需要维护节点的平衡性，更加复杂

MySQL主要使用B+树来作为索引结构。

```