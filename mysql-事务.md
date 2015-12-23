## MYSQL中的事务(下面说的都是基于InnoDB引擎)

### MYSQL中设置事务级别的方法
`set [session|global] transaction isolation level [read uncommitted|read commited|repeatable read|serializable]`;
比如：

	set session transaction isolation level read uncommitted
开启一个事务：`start transaction`

提交一个事物：`commit`

回滚一个事务：`rollback`

### 可能碰到的场景
* session1事务级别是`repeatable-read`，插入1条数据，session2的事务级别是`read-uncommitted`，无论session2是否开启事务，当session1事务插入一条数据的时候，不管是否提交都是可以看见的。
* session1和session2的事务级别都是`repeatable-read`，session1插入、更新、删除数据提交之前，不管session2是否开启事务都是不可见的。
* session1和session2事务级别都是`repeatable-read`，session1删除、更新的数据，session2更新、删除的时候会处于等待状态，如果session1一直不提交，session2中会抛出超时异常。
* session1和session2事务级别都是`repeatable-read`，session1和session2都开启事务，session1提交的东西session2是看不见的。
* session1和session2事务级别都是`read commited`，session1和session2都开启事务，session1修改一个字段的值，提交后，session2是可以看到的。
