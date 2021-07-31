---
layout: default
title: Transactions
parent: Hybris
has_children: false
---

# Transactions

{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Transaction

- transaction is a series of statements on a database that belong together in a certain context
- executed either in full or not at all, which keeps the database in a consistent state at any time
- Transactions are `ACID` (atomic, consistent, isolated, durable)
- Hybris provides implementation of spring `PlatformTransactionManager` so you can use:
  - @Transaction
  - tx xml schema
  - TransactionTemplate
  - Or direct hybris API

- The JTA UserTransaction interface is not provided
- hybris doesn’t participate in global transactions of multiple systems
- Two ways of implementations
  - SAP Transaction
  - Spring Transaction

## SAP Transactions

- only one single Transaction object for every single thread
- To get SAP Commerce `Transaction`

```java
Transaction tx = Transaction.current();

tx.begin(); 
boolean success = false;
try
{
   // do business logic
   doSomeBusinessLogic();
   success = true;
}
finally
{
   if( success )
      tx.commit();
   else
      tx.rollback();
}
```

- nested transaction does not start an individual transaction, but is executed in the context of the transaction of the calling method

```java
void doSomethingBig()
  {
     ..begin();
     doSomethingInTX();
     ..commit();         //commit executed because not nested
  }

  void doSomethingInTX()
  {
      ...begin();        //begins a nested TX
                         //will NOT throw error because tx is already running
      do()
      ...
      commit();         //commit ignored because inside nested TX
  }
```

### TransactionBody

- To avoid any problems with the above template, 

```java
Transaction.current().execute( new TransactionBody()
   {
      public Object execute()
      {
         doSomething();
         return something;
      }
   }
);
```

### Delayed Store

- all database modification statements done by a transaction are kept in memory until the transaction is actually flushed and then executed at once
- allows ts.optimizing (and possibly reducing) the set of database modification statement
- disadavantage : cause FlexibleSearch statements to return obsolete values because the database still returns old and stale content.


```java
Transaction.current().begin();
Transaction.current().enableDelayedStore(true);

tx.flushDelayedStore();
```

```properties
transaction.delayedstore= true
```

### Transaction Isolation

- Isolation level is fixed to `READ_COMMITED`
  - you can’t change that with `@Transactional` annotation (ignored)

## Spring Transactions

- `HybrisTransactionManager implements PlatformTransactionManager`
  - APO style Transaction using `@Transaction`

```xml
<bean id="fooService" class="x.y.service.DefaultFooService"/>
<tx:advice id="txAdvice" transaction-manager="txManager">
  <tx:attributes>
    <tx:method name="get*" read-only="true"/>
    <tx:method name="*"/>
  </tx:attributes>
</tx:advice>
<aop:config>
  <aop:pointcut id="fooServiceOperation" expression="execution(* x.y.service.FooService.*(..))"/>
  <aop:advisor advice-ref="txAdvice" pointcut-ref="fooServiceOperation"/>
</aop:config>
```

  - `TranscationTemplate`
```java
// 1.
PlatformTransactionManager manager=(PlatformTransactionManager) Registry.getApplicationContext().getBean("txManager");
// 2.
TransactionTemplate template = new TransactionTemplate(manager);
// 3.
template.execute(new TransactionCallbackWithoutResult()
{
  @Override
  protected void doInTransactionWithoutResult(final TransactionStatus status)
  {
    // do something transactional
  }
});
```
