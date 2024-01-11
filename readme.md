# Databases Deepdive 

- [ORMs](#orms)
- [ACID](#acid)
- [N+1 Problem](#n1-problem)
- [Normalization](#normalization)
- Failure Modes
- Profiling Performance

## ORMs
- Object-Relational Mapping (ORM) is a technique that lets you query and manipulate data from a database using an object-oriented paradigm. When talking about ORM, most people are referring to a library that implements the Object-Relational Mapping technique.  

## ACID 
- ACID are the four properties of relational database systems that help in making sure that we are able to perform the transactions in a reliable manner.
  - when you're building an application, the last thing you want to worry about is data integrity. 
- Its an acronym which refers to the presence of: atomicity, consistency, isolation and durability. 
- Whats an ACID compliant database? 
  - A transaction is a series of logically grouped database operations: inserting a row, updating a record, stuff like that. 
    - Things can go wrong when making transactions: losing connection to a remote instance, value errors, etc.
    - "Dirty Reads": if a transaction is updating data and hasn't committed yet, and another transaction is allowed to read the uncommited data, thats 'dirty' and could end up in the app showing incprrect data
    - "Non-Repeatable Reads": two consecutive reads in one transaction with a concurrent update in between, those reads are going to show different results even though they’re part of the same transaction
    - "Phantom Reads": if our second user adds content in between our first user’s two reads, the first read will be missing data that appears in the second read - very similar to non-repeatable read 
  - Popular relational dbs like MySQL avoid these transaction issues by following principals that govern how transactions work, by confirming to ACID. 
    - Atomicity: the 'all or nothing' rule - the transaction happens completely or not at all
    - Consistency: data is consistent before and after a transaction without any missing steps 
    - Isolation: multiple transactions can happen concurrently without reading the wrong data
    - Durability: transactional success is robust to system failure
  - The above overlap a lot, but remember the point of any ACID compliant DB is to make sure that:
    - Transactions can fail without hurting data integrity 
    - Multiple transactions can occur concurrently without reading and writing the wrong data 
  - How do the ACID sql db's we use achieve ACID compliance? 
    - They use a system called 'locking' to keep the DB on hold while transactions happen
    - when a transaction begins: the db locks the data that its working with until the transaction is completed 
      - once it begins and acquires a lock, it can either finish successfully and commit or run into an error and abort 

- ACID in NoSQL
  - many NoSQL db's are built as distributed systems and they can't always ensure complete transactional consistency. 
  - CAP theorem: "in distributed systems, you can never have full consistency and full availability; you need to choose one"
  - a new quasi-standard emerged for NoSQL DB's called BASE - its a weak/soft consistency model that relaxes some assumptions of ACID in order to achive scalability:
    - Basic Availability: the db basically works most of the time, evne though its not perfect
    - Soft-State: nodes of the db aren't necessarily consistent with each other all the time 
    - Eventual consistency: data will be consistent across nodes eventually, like by read time
  - kind of the opposite of SQL databases - it prioritises availability over perfect consistency

## N+1 Problem
- The N + 1 query problem happens when your code executes N additional query statements to fetch the same data that could have been retrieved when executing the primary query
- How to fix it?
  - batch elements retrieval when relevant: i.e. as you fetch everything you need in your pantry in a single batch before starting to cook, your code should preload dependent models when needed 

## Normalization 
- database normalization is the process of structuring a relational database in accordance with a series of so-called normal forms in order to reduce data redundancy and improve data integrity. 
  - it entails organizing the columns (attributes) and tables (relations) of a database to ensure that their dependencies are properly enforced by database integrity constraints
  - it is accomplished by applying some formal rules either by a process of synthesis (creating a new database deisgn) or decomposition (improving an existing database design)
  - there are 6 'normal form's, however in most practical application, normalization achieves its best in 3rd normal form
  - https://www.guru99.com/database-normalization.html


An example of a non-normalized db table:
| FULL NAMES  | PHYSICAL ADDRESS | MOVIES RENTED                                          | SALUTATION |
|-------------|------------------|--------------------------------------------------------|------------|
| Janet Jones | 4 fake street    | Pirates of the Caribbean, Clash of the titals, Pokemon | Ms.        |
| Robert Phil | 123 real street  | Toy Story, Land before time                            | Mr.        |

1NF (first normal form) rules:
1. each table cell should contain a single value
2. each record needs to be unique 

1NF example: 
| FULL NAMES  | PHYSICAL ADDRESS | MOVIES RENTED            | SALUTATION |
|-------------|------------------|--------------------------|------------|
| Janet Jones | 4 fake street    | Pirates of the Caribbean | Ms.        |
| Janet Jones | 4 fake street    | Clash of the titals      | Ms.        |
| Janet Jones | 4 fake street    | Pokemon                  | Ms.        |
| Robert Phil | 123 real street  | Toy Story                | Mr.        |
| Robert Phil | 123 real street  | Land before time         | Mr.        |


- Other things to note:
  - What is a KEY in SQL? 
    - A key in SQL is a value used to identify records in a table uniquely 
    - an SQL key is a single column or combination of multiple columns used to uniquely identify rows or tuples in the table 
    - often used to identify duplicate information and establish a relationship between multiple tables in the db
  - What is a primary Key?
    - A single column value used to identify a database record uniquely 
    - Cannot be null
    - Must be unique 
    - Should rarely be changed
    - must be given a value when a new record is inserted
  - What is a Composite Key?
    - a primary key composed of multiple columns used to identify a record uniquely 
    - i.e. there might be two people with the name 'Robert Phil' so we cannot use that value. But we could use a combination of 'Robert Phil' and the address, as this should be unique. 
  - What is a Foreign Key?
    - References the primary key of another table - helps to connect your tables
      - if there is not a primary key of that value in the other table it will error - this helps referrential integrity.
    - A foreign key can have a different name from its primary key
    - ensures rows in one table have corresponding rows in another
    - they do not have to be unique (unlike primary key) - most often they aren't
    - foreign keys can be null even though primary keys can not 

2NF rules:
1. Be in 1NF
2. Single column primary key that does not functionally depend on any subset of candidate key relation 

2NF example: 
- now split into two tables to satisfy the 2nd rule
- MEMBERSHIP ID in the first table is the primary key
- MEMBERSHIP ID in the second table is the foreign key

| MEMBERSHIP ID | FULL NAMES  | PHYSICAL ADDRESS | SALUTATION |
|---------------|-------------|------------------|------------|
| 1             | Janet Jones | 4 fake street    | Ms.        |
| 2             | Robert Phil | 123 real street  | Mr.        |

| MEMBERSHIP ID | MOVIES RENTED            |
|---------------|--------------------------|
| 1             | Pirates of the caribbean |
| 1             | Clash of the titans      |
| 1             | Pokemon                  |
| 2             | Toy story                |
| 2             | Land before time         |


Other things to note: 
  - Transitive functional dependencies
    - this is when changing a non-key column may cause any other of the non-key columns to change 
    - i.e. if you change the name 'Robert Phil', this pay change the Salutation

3NF Rules: 
1. Be in 2NF
2. Has no transitive functional dependencies 

3NF Example:
- we have again divided our tables and now we have one which stores salutations
- there is no transitive functional dependencies, hence our tables is in 3NF 
- in the first table, salutation id is the foreign key. in the 3rd table salutation id is the primary key.

| MEMBERSHIP ID | FULL NAMES  | PHYSICAL ADDRESS | SALUTATION ID |
|---------------|-------------|------------------|---------------|
| 1             | Janet Jones | 4 fake street    | 1             |
| 2             | Robert Phil | 123 real street  | 2             |

| MEMBERSHIP ID | MOVIES RENTED            |
|---------------|--------------------------|
| 1             | Pirates of the caribbean |
| 1             | Clash of the titans      |
| 1             | Pokemon                  |
| 2             | Toy story                |
| 2             | Land before time         |

| SALUTATION ID | SALUTATION |
|---------------|------------|
| 1             | Ms.        |
| 2             | Mr.        |


## Failure Modes 

## Profiling Performance 