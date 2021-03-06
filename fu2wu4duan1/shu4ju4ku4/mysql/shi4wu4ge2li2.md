## MySQL事务隔离

事务并发时会出现脏读、不可重复读和幻读等情况。为了保证并发时操作数据的正确性，数据库都会有事务隔离级别的概念。

脏读：一个事务正在访问数据，并且对数据进行了修改，但是这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。

不可重复读：在一个事务还没有结束时，另外一个事务也访问了该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。

幻读：当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样。