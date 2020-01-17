## mysql学习笔记

+ ### **mvcc**

  > Multiversion (version) concurrency control (MCC or MVCC) 多版本并发控制 ，它是数据库管理系统一种常见的并发控制。

  

  + 锁
    1. 悲观锁： 当一个线程需要对共享资源进行操作的时候，首先对共享资源进行加锁，当该线程持有该资源的锁的时候，其他线程对该资源进行操作的时候会被 **阻塞**。比如 Java 中的 Synchronized 关键字。
    2. 乐观锁：当一个线程需要对一个共享资源进行操作的时候，不对它进行加锁，而是在操作完成之后进行判断。(比如乐观锁会通过一个版本号控制，如果操作完成后通过**版本号**进行判断在该线程操作过程中是否有其他线程已经对该共享资源进行操作了，如果有则通知操作失败，如果没有则操作成功)，当然除了 版本号 还有 CAS，

***1. 在InnoDB引擎下创建一个表，会自动创建三个隐藏列：***

> 1. RowID：隐藏的自增ID，当建表没有指定主键，InnoDB会使用该RowID创建一个聚簇索引。
> 2. DB_TRX_ID：最近修改（更新/删除/插入）该记录的事务ID。
> 3. DB_ROLL_PTR：回滚指针，指向这条记录的上一个版本。

而 MVCC 使用的是其中的 **事务字段，回滚指针字段，是否删除字段**。

***2. undoLog***

> 事务的回滚日志，是 可见性算法 的非常重要的部分，分为两类。 insert undo log：事务在插入新记录产生的undo log，当事务提交之后可以直接丢弃 update undo log：事务在进行 update 或者 delete 的时候产生的 undo log，在快照读的时候还是需要的，所以不能直接删除，只有当系统没有比这个log更早的read-view了的时候才能删除。ps：所以长事务会产生很多老的视图导致undo log无法删除 大量占用存储空间。

***3. read-view***

> 读视图，是MySQL秒级创建视图的必要条件，比如一个事务在进行 select 操作(快照读)的时候会创建一个 read-view ，这个read-view 其实只是三个字段。 alive_trx_list(我自己取的)：read-view生成时刻系统中**正在活跃的事务id**。 up_limit_id：记录上面的 alive_trx_list 中的**最小事务id**。 low_limit_id：read-view生成时刻，**目前已出现的事务ID的最大值 + 1**。

# mvcc 版本控制就是基于以上三个功能来实现

在一事务开启快照读的时候（select **field** from **table** where **条件**）会生成一个read-view 记录一下**当前的活跃事务id**

> ```shell
> up-limit-id: 当前活跃务中最小的id
> active-trx-list: (我自己随便起的名，差不多就行了)当前活跃的事务
> low-limit-id: 当前出现的最大事务id + 1
> ```

在去读记录的时候会拿到当前记录的 DB-TRX-ID 去和READ-VIEW中的字段进行比较：

> ````.shell
> 1. 先和up-limit-id 进行比较，如果 db-trx-id 小于或者等于up-limit-id，则认为当前事务是可见的，会直接返回这行记录。如果大于则继续往下进行比较
> 2. 再进行判断db-trx-id是否大于等于low-limit-id，如果大于等于则说明此事务不可能看见该记录，进行回滚。如果小于则继续往下比较
> 3. 最后判断db-trx-id是不是在active-trx-list中，如果不在此列表中说明已经提交，会直接返回记录，如果是在的则说明此事务还没有进行提交就是不可见的状态。进行回滚。
> ````
>
> **进行回滚**
>
> 会通过DB_ROLL_PTR回滚到这条记录的上一个版本

每次的修改/删除操作都会通过undo log 进行记录，形成一个版本链，每一个事务修改一次都会在undo log 上的增加一个新的版本，则此记录的db-trx-id就是此事务的id， db-roll-ptr 就是指向下一版本的内存地址。

这样mvcc 就能实现了，总结：

每一记录都会有三个隐藏的字段：ROW-ID , DB-TRX-ID,  DB-ROLL-PTR。在多事务快照读的时候，会生成一个read-view来记录当前事务的id， 通过可见性算法来找出该事务对应的记录。所有事务的修改都会被记录到一个undo log日志中，形成一个版本链。

## 例子：

| id   | name | DB-TRX-ID | DB-ROLL-PTR |
| ---- | ---- | --------- | ----------- |
| 1    | 小强 | 2         | 0x122341    |
| 1    | 小明 | 1         | null        |

事务A（id=2）,事务B（id=3），事务C（id=4）

事务A把name改成`张三`但未提交，事务B开始查（select * from table where id = 1）,则事务B查出的还是`name=小强`

过程：

事务B开始查的时候，会读出db-trx-id = 2，同时会生成一个`read-view`记录一下`up-limit-id=2 `active-trx-id=[2,3]` `low-trx-id=3 + 1=4` ，判断`2(当前记录db-trx-id)<2(up-limit-id)`不符合，并且`2(当前记录db-trx-id)!=3(当前事务Bid=2)，则进行下一步判断。

判断`2（当前记录db-trx-id）>=4（low-limit-id）`不符合，再进行下一步判断

判断`2（当前记录db-trx-id）`是否在`active-trx-list`中，发现是在此集合中，则说明此事务还没有进行提交，则会根据`db-roll-ptr`在`undo-log`中找出一下版本的`db-trx-id =1`

再进行判断`1(当前记录db-trx-id)<2(up-limit-id)`符合此条件，说明此版本是可见的，则会找出此记录，最后的`name`就是**`小明`**