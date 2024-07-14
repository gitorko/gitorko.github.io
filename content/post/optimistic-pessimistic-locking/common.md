Locking ensures that the row is not concurrently updated by 2 different threads which might corrupt the data.

**Problem:**

Thread A: Reads row with amount 100$ in Transaction T1
Thread B: Reads row with amount 100$ in Transaction T2
Thread A: Adds 10$, new amount is 110$
Thread B: Adds 10$, new amount is still 110$ instead of 120$.

**Solution 1 (Optimistic Locking):**

Thread A: Reads row with amount 100$ in Transaction T1
Thread B: Reads row with amount 100$ in Transaction T2
Thread A: Adds 10$, new amount is 110$
Thread B: Adds 10$ and tries to save but sees that the record is not the same record that it read. So fails & does retry.

**Solution 2 (Pessimistic Locking):**

Thread A: Reads row with amount 100$ in Transaction T1, it holds a row level lock.
Thread B: Reads row in Transaction T2 but is blocked as T1 holds a lock, So it waits till timeout happens & retry.
Thread A: Adds 10$, new amount is 110$
Thread B: Reads row with updated amount 110$ and updates to 120$

**Types of locking**

1. Pessimistic Locking - Locks held at row level or table level. Not ideal of high performance & cant scale.
2. Optimistic Locking - Version field is added to the table, JPA ensures that version check is done before saving data, if the version has changed then update will throw Error. Ideal for high performance & can scale.

### Pessimistic locking

1. `LockModeType.PESSIMISTIC_READ` - Rows are locked and can be read by other transactions, but they cannot be deleted or modified. PESSIMISTIC_READ guarantees repeatable reads.
2. `LockModeType.PESSIMISTIC_WRITE` - Rows are locked and cannot be read, modified or deleted by other transactions. For PESSIMISTIC_WRITE no phantom reads can occur and access to data must be serialized.
3. `LockModeType.PESSIMISTIC_FORCE_INCREMENT` - Rows are locked and cannot be read, modified or deleted by other transactions. it forces an increment of the version attribute

### Optimistic locking

1. `LockModeType.OPTIMISTIC` - Checks the version attribute of the entity before committing the transaction to ensure no other transaction has modified the entity.
2. `LockModeType.OPTIMISTIC_FORCE_INCREMENT` - Forces a version increment of the entity, even if the entity has not been modified during the update.

## Transaction Isolation

Transaction isolation levels in JPA define the degree to which the operations within a transaction are isolated from the operations in other concurrent transactions
JPA, typically using the underlying database and JDBC settings

1. `Isolation.READ_UNCOMMITTED` Read Uncommitted - The lowest level of isolation. Transactions can read uncommitted changes made by other transactions.
2. `Isolation.READ_COMMITTED` Read Committed - Transactions can only read committed changes made by other transactions.
3. `Isolation.REPEATABLE_READ` Repeatable Read - If a transaction reads a row, it will get the same data if it reads the row again within the same transaction.
4. `Isolation.SERIALIZABLE` Serializable - The highest level of isolation. Transactions are completely isolated from one another.

**Data Consistency**

1. Dirty reads: read UNCOMMITED data from another transaction.
2. Non-repeatable reads: read COMMITTED data from an UPDATE query from another transaction.
3. Phantom reads: read COMMITTED data from an INSERT or DELETE query from another transaction.

**Dirty Read**

| NAME | AGE |
|:-----|:----|
| Bob  | 35  |

| TRANSACTION T1                                 | TRANSACTION T2                                |
|:-----------------------------------------------|:----------------------------------------------|
| select age from table where name = 'Bob'; (35) |                                               |
|                                                | update table set age = 40 where name = 'Bob'; |
| select age from table where name = 'Bob'; (40) |                                               |
|                                                | commit;                                       |

Non-Repeatable Read

| NAME | AGE |
|:-----|:----|
| Bob  | 35  |

| TRANSACTION T1                                 | TRANSACTION T2                                |
|:-----------------------------------------------|:----------------------------------------------|
| select age from table where name = 'Bob'; (35) |                                               |
|                                                | update table set age = 40 where name = 'Bob'; |
|                                                | commit;                                       |
| select age from table where name = 'Bob'; (40) |                                               |

Phantom Read

| NAME | AGE |
|:-----|:----|
| Bob  | 35  |

| TRANSACTION T1                                 | TRANSACTION T2                         |
|:-----------------------------------------------|:---------------------------------------|
| select count(*) from table where age = 35; (1) |                                        |
|                                                | insert into table values ('jack', 35); |
|                                                | commit;                                |
| select count(*) from table where age = 35; (2) |                                        |

| Isolation Level  | Dirty | Non-Repeatable Reads | Phantom Reads | 
|:-----------------|:------|:---------------------|:--------------|
| Read Uncommitted | Yes   | Yes                  | Yes           |
| Read Committed   | No    | Yes                  | Yes           |
| Read Committed   | No    | No                   | Yes           |
| Serializable     | No    | No                   | No            |

```yaml
spring:
  jpa:
    properties:
      hibernate:
        connection:
          isolation: 2
```

```bash
@Transactional(isolation = Isolation.SERIALIZABLE)
```

```sql
SHOW default_transaction_isolation;
```