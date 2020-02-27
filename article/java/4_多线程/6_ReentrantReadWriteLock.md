# ReentrantReadWriteLock（读写锁）

ReentrantReadWriteLock支持以下功能：

1. 支持公平和非公平的获取锁的方式；
1. 支持可重入。读线程在获取了读锁后还可以获取读锁；写线程在获取了写锁之后既可以再次获取写锁又可以获取读锁；
1. 还允许从写入锁降级为读取锁，其实现方式是：先获取写入锁，然后获取读取锁，最后释放写入锁。但是，从读取锁升级到写入锁是不允许的；
1. 读取锁和写入锁都支持锁获取期间的中断；
1. Condition支持。仅写入锁提供了一个 Condition 实现；读取锁不支持 Condition ，readLock().newCondition() 会抛出 UnsupportedOperationException。 