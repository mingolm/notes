## mysql 事务

> mysql 多种存储引擎支持事务，包括 innodb/bdb，myisam 不支持事务，粗略地说，innodb 通过 UNDO/ REDO log 来实现事务



```shel
UNDO log: 事务开始前，记录初始数据，用于事务失败时数据的回滚
REDO log: 事务执行过程中的数据记录，当事务 commit 时，将 REDO log 内容刷新至磁盘
```





#### 事务的特性（ACID）

- Atomicity/原子性：事务不可分割，同时失败或成功

  - 通过 UNDO log 保证，执行过程中需要 rollback，则通过 UNDO log 进行 update

- Consistency/一致性：状态总是一致，由一种状态至另一种状态的改变

- Isolation/隔离性：事务执行期间对另一个事务不可见，通过锁实现

  - 多个事务操作一条数据，可能出现 不可重复读/脏读/幻读，因此 mysql 支持了四种隔离级别，级别越高事务隔离性越好，但性能就越低
    - read uncommit(读未提交/脏读)
      - 事务B 提前读取到事务A 还未 commit 的数据，事务A 进行 rollback，此隔离级别下，读不加锁，写会加排他锁
    - read commit (读已提交)
      - MVCC 机制，基于数据级别的快照，有不可重复读的问题，A 事务多次读取同一行数据可能不一致，过程中B 事务可能进行 commit，导致版本号更新
    - repeatable read (可重复复读)
      - MVCC 机制，基于事务级别的快照，解决了不可重复读，幻读
    - serializable (串行)
      - 多个事务不可同时执行，性能最差，但是最安全

- Durability/持久性：事务提交后，数据永久存储

  - 通过 REDO log 保证，事务提交后，mysql 首先写入内存，然后写入 REDO log，此时 mysql 宕机，则会从 REDO log 中恢复数据

  

#### MVCC 多版本并发控制

> 在 read commit/ repeatable read 两种隔离级别下生效，mysql 事务过程中为了解决脏读，直接的方式是加读锁，但是性能也会下降，mysql 为了解决性能问题引入了 MVCC

简单的说就是在事务读取的时候，加一个版本号，仅在其它事务 commit 的时候，会生成一个新的版本号



