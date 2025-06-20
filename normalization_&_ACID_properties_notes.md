# Notes on Normalization and ACID Properties

## ACID PROPERTIES

These stand for Atomicity, Consistency, Isolation and Durability.

They are a set of properties that ensure reliable processing of database transactions.

They define how transactions should be handled in a reliable manner, ensuring the database is consistent even when there are issues or concurrent access.

> _Transactions_ are operations performed as a a single unit in a database, such as reading and writing data in a database.

### ATOMICITY

> Comes from the word atom, which usually represents a singular unit.

Atomicity is a property of databases that ensures that a transaction, as a whole, is either successful or if it isn't, none of the operations of the transaction are carried out.

It prevents partial or fractional implementation.

If a transaction was not treated atomically it would lead to inaccurate data, data inconsistencies and partial updates.

#### Example1

> _In a bank, money transfers between 2 accounts_
> If from the sender, the operation is successful, and the receiving operation fails, then the entire transaction will be rolled back, and the first account will not be charged.

We accomplish atomicity through defining transaction boundaries, with commands like `BEGIN`.

#### Practical Example(Atomicity)

In the example below, if the transaction does not work out, a rollback occurs, ensuring atomicity.

```sql
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
COMMIT;
```

Databases use methods such as transaction logs, and rollback processes to maintain atomicity.

--find out about transaction logs:COMMIT
--also rollback processes:ROLLBACK

### CONSISTENCY

This is an ACID property that ensures that a transaction brings a database from one valid state to another while ensuring that the data aligns to the predefined rules, constraints and integrity checks.

Consistency is maintained by implementing primary keys, foreign keys, unique keys and checks.

If the rules are not followed, the transaction is cancelled.

#### Example2

> Like in a bank transfer, the currency value on the sender side should be the same as the currency value on the recipient's side, this ensures consistency in the database.
> This example refers to constraints, such as datatype.

Consistency is enforced through validation keys, database triggers, and constraints...these ensure the logical rules at the database level.

### ISOLATION

This is a property of ACID in a database that ensures that transactions execute independently without interrupting or affecting each other.

> For example, when many users access or try to update or alter the database at the same time.
> One user might end up accidentally over writing another user's work!

#### Isolation levels

These levels help in determining the level of separation between transactions.

- Read uncommitted: this is the lowest level and transactions may lead to 'dirty reads'.
- Read committed: transactions here can only access committed data, which leads to less 'dirty leads' but not 'phantom reads'.
- Repeatable Read: This ensures consistency
- Serializable: This is the highest level, and this fully isolates the transactions.
  It prevents 'dirty reads', 'non-repeatable reads', and 'phantom reads' by locking data

Here are **references**:

- Dirty Reads: Is when a transaction reads data that has been modified by another transaction, but hasn't been committed.
- Non-repeatable Reads: This usually happens when a transaction reads the same row twice and gets different values.
- Phantom Reads: these occurs when, in the course of a transaction, two identical queries are executed, and the collection of rows returned by the second query is different from the first.

Isolation is achieved through locking protocols, which prevents concurrent access to the same data.

#### Practical Example(Isolation)

Using Repeatable Read:

```sql
-- Transaction 1
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ
BEGIN TRANSACTION
SELECT Quantity FROM Products WHERE Id = 1001

-- Here there be some delay as we assume were doing something else
WAITFOR DELAY '00:00:15'
SELECT Quantity FROM Products WHERE Id = 1001
COMMIT TRANSACTION

-- Transaction 2
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ
UPDATE Products SET Quantity = 5 WHERE Id = 1001
```

### DURABILITY

This is a property that ensures that once a transaction has been committed it doesn't just disappear for no apparent reason.

It ensures that the transaction commit remains permanently, even during power outages or system outages.

Durability is accomplished through write-ahead logging, and backup mechanisms. These methods help confirm that the committed changes are permanently stored.

### Practical Example(Durability)

We use write-ahead logging, which writes changes to a log before applying them to the database.

```sql
ALTER SYSTEM SET wal_level = 'replica';
```

We also use `COMMIT` to log changes.

```sql
BEGIN TRANSACTION;

INSERT INTO orders (order_id, customer_id, amount)
VALUES (1, 101, 500);

COMMIT;
```

### Advantages of ACID properties

- Concurrency control.
- Recovery: ensure that in case of an issue, the data of the database can be recovered, before the point of failure.
- Ensure data integrity: they cannot lose the changes made to the database.
- They ensure data consistency: where data remains accurate after all transactions and operations.

### Disadvantages of ACID properties

- Scalability Difficulty, especially when it's a system with high volume rates.
- Performance Overhead: it takes a lot of processing and expertise to enforce isolation and all the properties simultaneously.

---

## NORMALIZATION IN DATABASES

Normalization, is simply the process in which we get rid of redundancy in a database's tables/relations and columns.

To achieve this we structure the data in such a way that attributes are grouped with the primary key that provides unique identification.

> On a light note, you'll realize that it's all about simplifying the table and column structures, which usually means making new tables from larger tables. No biggie.

**Un-normalized** data is usually a combo of a variety columns representing different entities, usually this can be quite disorganized, or too much information.

Just to note:_Sometimes un-normalized/denormalized is needed for data analysis_

### Reasons for Normalization

Normalization's main goal is to **eliminate duplicate data** and ensure everything is stored in a logical way.
Normalization ensures dependencies for tables and columns are enforced by database integrity constraints (simply Primary and Foreign keys).

### Levels of Normalization

Normalization has several levels which fix specific types of redundancy and dependencies in a database.

Common forms:

- 1NF
- 2NF
- 3NF

Higher forms:

- BCNF-Boyce Codd Normal Form
- 4NF
- 5NF

#### First Normal Form

Ensures that the database tables are organized in such a way that each column contains atomic (indivisible) values.

Each record should be unique.

This eliminates repeating groups(transitive dependencies), and hence structuring the database into tables and columns.

> **Transitive dependencies:** This is a dependency that occurs when a non-key attribute depends on another non-key attribute, which now depends on the primary key.

#### Second Normal Form

This is an improvement of 1NF, where we need to remove redundant data from a table that is being applied to multiple rows, then we place them in separate tables.

This usually requires all non-key attributes to be fully functional on the primary key.

This eliminates partial dependencies.

> **Partial dependency:** This is a situation that occurs when a non-prime attribute (i.e., not part of any candidate key) is functionally dependent on only a part of the primary key, rather than the entire primary key.

#### Third Normal Form

This is also, yet, an improvement of 2NF, where we ensure that all non-key attributes are not only fully functional on the primary key, but also independent of each other.

This now eliminates transitive dependency.

> **Transitive dependency:** is an indirect relationship between values in the same table that causes a functional dependency. This type of dependency occurs when one non-primary attribute depends on another non-primary attribute, which in turn depends on the primary key.

Usually, normalization, in most practical situations, achieves its best in the **3<sup>rd</sup> Normal Form**.

#### Boyce-Codd Normal Form

Is an enhancement of 3NF, that fixes the anomalies not handled in 3NF.

It requires every determinant to be a candidate key, ensuring that there's even stricter following of normalization rules.

#### Fourth Normal Form

This level of normalization addresses multi-valued dependencies.

Here, we will ensure that there are no multiple independent multi-valued facts about an entity in a record.

### FIfth Normal Form

This is also known as `Projection-Join Normal Form`.because it is based on the re-construction of information from smaller, differently arranged data pieces.

---

## REFERENCES

[DataCamp](https://www.datacamp.com/tutorial/normalization-in-sql)

[Geek For Geeks](https://www.geeksforgeeks.org/acid-properties-in-dbms/)

[MIT notes](https://www.cs.uct.ac.za/mit_notes/database/pdfs/chp08.pdf)

[Relia Software](https://reliasoftware.com/blog/acid-properties-in-dbms)

[Stack Overflow](https://stackoverflow.com/questions/11043712/non-repeatable-read-vs-phantom-read)
