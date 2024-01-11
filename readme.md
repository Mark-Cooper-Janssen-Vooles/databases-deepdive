# Databases Deepdive 

- ORMs
- ACID 
- Transactions
- N+1 Problem
- Normalization
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

## Normalization 

## Failure Modes 

## Profiling Performance 