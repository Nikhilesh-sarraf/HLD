In **HLD (High-Level Design)**, **Monolithic Architecture** and **Microservices Architecture** are two ways to design a system/application.

# 1. Monolithic Architecture

A monolithic application is built as **one single unit** where all components are tightly connected.

Example modules:

* User Authentication
* Product Service
* Payment Service
* Notification Service
* Database Access

Everything runs in one application.

### Structure

```text
+--------------------------------+
|         Single Application     |
|                                |
|  Auth | Product | Payment      |
|  Notification | Database       |
|                                |
+--------------------------------+
                |
                ↓
           Single Database
```

### Characteristics

✅ Single codebase
✅ Single deployment
✅ Easier to develop initially
❌ Components tightly coupled

### Advantages

1. Simple to develop
2. Easy debugging
3. Easy deployment
4. Suitable for small projects

### Disadvantages

1. Difficult to scale individual components
2. Large codebase becomes difficult to maintain
3. One bug can affect the whole application
4. Slower development for large teams

---

### Real Example

Small e-commerce application:

```text
E-commerce App
    |
    ├── Login
    ├── Products
    ├── Orders
    ├── Payment
    └── Notifications
```

Everything exists in one application.

---

# 2. Microservices Architecture

Microservices split the application into **small independent services**.

Each service:

* Has its own logic
* Can have its own database
* Can be deployed separately

### Structure

```text
          Client
             |
             ↓
      +---------------+
      |  API Gateway  |
      +---------------+
      /      |      \
     /       |       \
    ↓        ↓        ↓

+--------+ +--------+ +---------+
| Auth   | |Product | |Payment  |
|Service | |Service | |Service  |
+--------+ +--------+ +---------+

     ↓          ↓         ↓
   DB1        DB2       DB3
```

---

### Characteristics

✅ Loosely coupled
✅ Independent deployment
✅ Independent scaling
❌ More complex

### Advantages

1. Easy to scale specific services

Example:

```text
Payment service → High traffic
Scale only payment service
```

2. Faster development with multiple teams

Example:

* Team A → Authentication
* Team B → Product
* Team C → Payment

3. Better fault isolation

If Notification service crashes:

```text
Notification ❌
Payment ✅
Products ✅
```

Other services still work.

---

### Disadvantages

1. Complex deployment
2. Network communication overhead
3. Distributed debugging is difficult
4. Data consistency becomes challenging
5. Requires monitoring tools

---

# Monolithic vs Microservices

| Feature           |         Monolithic |          Microservices |
| ----------------- | -----------------: | ---------------------: |
| Codebase          |             Single |               Multiple |
| Deployment        |     One deployment | Independent deployment |
| Scaling           | Entire application |    Individual services |
| Database          |        Usually one |     Often separate DBs |
| Complexity        |                Low |                   High |
| Development speed |     Fast initially | Faster for large teams |
| Maintenance       | Hard as size grows |                 Easier |
| Failure impact    |          Whole app |       Isolated service |
| Best for          |     Small projects |          Large systems |

---

# HLD Interview Perspective

Interviewers often ask:

**"When should you use microservices?"**

Answer:

Use microservices when:

* System becomes very large
* Different modules scale differently
* Multiple teams work simultaneously
* Independent deployment is needed

Use monolithic when:

* Startup/MVP
* Small application
* Small team
* Limited infrastructure

---

Typical evolution in companies:

```text
Startup
   ↓
Monolithic App
   ↓
Application grows
   ↓
Break into Microservices
   ↓
Large distributed system
```

Examples:

* Early startups → Monolithic
* Large systems like Netflix and Amazon → Microservices

For HLD interviews, remember this one line:

**Monolith = simple but tightly coupled**
**Microservices = scalable but operationally complex**



**Command Query Responsibility Segregation (CQRS)** is a design pattern used in **System Design/HLD** where **read operations and write operations are separated**.

* **Command → modifies data (Create, Update, Delete)**
* **Query → reads data**

Instead of one system handling both, CQRS creates separate paths.

# Traditional Architecture (without CQRS)

Single service handles both reads and writes:

```text
          Client
             |
             ↓
     +----------------+
     | Application    |
     | Service        |
     +----------------+
             |
             ↓
         Database
```

Problems:

* Read and write traffic use the same resources
* Hard to scale independently
* High load affects performance

Example:

Suppose an e-commerce site has:

* 1 million product searches/day
* 50,000 orders/day

Read traffic is much larger than write traffic.

---

# CQRS Architecture

Separate read and write systems.

```text
                  Client
                     |
         +-----------+------------+
         |                        |
         ↓                        ↓
   Command Side             Query Side
   (Write Side)             (Read Side)

Create Order              Get Orders
Update Product            Search Product

         ↓                        ↓
    Write Database          Read Database
```

---

## Command Side (Write Side)

Purpose:

* Modify data

Operations:

```text
Create User
Update Product
Delete Order
Place Order
```

Example API:

```text
POST /orders

PUT /products/10

DELETE /users/5
```

Characteristics:

* Business logic heavy
* Validation required
* Ensures consistency

---

## Query Side (Read Side)

Purpose:

* Fetch data only

Operations:

```text
Get Product
Search User
Get Order History
```

Example API:

```text
GET /products

GET /orders/101
```

Characteristics:

* Optimized for fast retrieval
* No business logic changes data

---

# Example: E-commerce System

Without CQRS:

```text
Client
   ↓
Order Service
   ↓
Database
```

With CQRS:

```text
                   Client
                      |
         +------------+-------------+
         |                          |
         ↓                          ↓

  Command Service           Query Service
   (Place Order)            (Get Orders)

         ↓                          ↓
     Write DB                  Read DB
         ↓
     Event Bus
         ↓
     Update Read DB
```

---

# Flow Example

User places an order:

### Step 1: Command

```text
POST /placeOrder
```

Command service:

* Validates order
* Saves to Write DB
* Publishes event

```text
OrderCreated Event
```

---

### Step 2: Event updates read side

```text
Write DB
    ↓
Event Queue
    ↓
Read DB Updated
```

---

### Step 3: Query

```text
GET /orders
```

Read service fetches data from Read DB.

---

# Advantages

✅ Better scalability

* Scale reads independently

```text
Read Service → 10 servers

Write Service → 2 servers
```

✅ Better performance

✅ Optimized databases

Example:

```text
Write DB → MySQL

Read DB → Elasticsearch
```

✅ Easier handling of heavy read traffic

---

# Disadvantages

❌ Increased complexity

❌ Data synchronization challenges

❌ Eventual consistency issues

Example:

```text
Order placed
      ↓
Read DB update delayed by 2 sec
```

User may not immediately see the new order.

---

# When to use CQRS?

Use CQRS when:

* Read traffic is much larger than write traffic
* Large-scale applications
* Complex business logic
* Microservices systems

Examples:

* Banking systems
* E-commerce websites
* Social media platforms

---

For HLD interviews, remember:

**CRUD + one DB → Traditional approach**
**Separate Read + Write → CQRS**



These are two very important HLD concepts and they are often asked together because **database sharding frequently uses consistent hashing**.

# 1. Database Sharding

Database sharding means **splitting a large database into smaller databases (shards)**.

Instead of storing everything in one DB:

```text
Users Database
----------------
User1
User2
User3
...
User10000000
```

Split it:

```text
Shard-1        Shard-2         Shard-3
--------       --------       --------
User1          User1001       User2001
User2          User1002       User2002
User3          User1003       User2003
```

Purpose:

* Reduce database load
* Improve scalability
* Increase performance

---

## Types of Sharding

### A) Range-based sharding

Data distributed by ranges.

```text
Shard 1 → User ID 1–1000

Shard 2 → User ID 1001–2000

Shard 3 → User ID 2001–3000
```

Problem:

If many users are in one range:

```text
Shard1 → 90% traffic
Shard2 → 5%
Shard3 → 5%
```

Uneven load.

---

### B) Hash-based sharding

Data distributed using hash function.

Formula:

```text
Shard = UserID % NumberOfShards
```

Example:

```text
UserID = 123

123 % 3 = 0

Store in Shard0
```

Distribution:

```text
Shard0 → User3, User6, User9

Shard1 → User1, User4, User7

Shard2 → User2, User5, User8
```

---

# Problem with Normal Hashing

Suppose:

```text
Shard = UserID % 4
```

Current system:

```text
4 servers

123 % 4 = 3
```

User goes to:

```text
Shard3
```

Now add another server:

```text
5 servers

123 % 5 = 3
```

Many users move to different shards.

Example:

```text
User10 : 10%4 =2
User10 : 10%5 =0
```

Almost all data needs redistribution.

This is expensive.

---

# 2. Consistent Hashing

Consistent hashing solves this problem.

Instead of:

```text
Hash(Key) % N
```

it uses a **circular hash space (hash ring)**.

---

### Hash Ring

```text
                    0
               /         \
             /             \
         DB1                 DB2
         /                     \
        /                       \
      DB4                      DB3
```

Both:

* Servers
* Data keys

are placed on the same ring.

---

### Rule

Data moves clockwise until it finds the first server.

Example:

```text
UserA hash = 40

Ring:

DB1=20
DB2=50
DB3=70
DB4=90
```

UserA:

```text
40 → nearest clockwise server → DB2
```

---

### Before adding server

```text
        DB1
      /     \
     /       \
   DB4       DB2
      \      /
       \    /
        DB3
```

---

### Add DB5

```text
        DB1
      /     \
     /       \
   DB4       DB2
     \         \
      DB5      DB3
```

Only nearby keys move:

```text
Old → DB2
New → DB5
```

Most data remains unchanged.

---

# Why consistent hashing is better

| Feature        |         Normal Hashing | Consistent Hashing |
| -------------- | ---------------------: | -----------------: |
| Add server     | Rehash almost all data | Small amount moves |
| Remove server  | Rehash almost all data | Small amount moves |
| Scalability    |              Difficult |               Easy |
| Load balancing |                 Medium |             Better |

---

# Virtual Nodes (Very Important Interview Topic)

Real systems improve consistent hashing using **virtual nodes**.

Instead of:

```text
DB1
DB2
DB3
```

Create:

```text
DB1-A
DB1-B
DB1-C

DB2-A
DB2-B
DB2-C
```

Ring:

```text
DB1-A → DB2-A → DB3-A → DB1-B → DB2-B ...
```

Benefits:

✅ Better load distribution
✅ Prevents one server becoming overloaded

---

# Complete HLD flow

```text
Client Request
       ↓
Hash(UserID)
       ↓
Consistent Hash Ring
       ↓
Find nearest server
       ↓
Shard Database
```

Interview one-line summary:

**Sharding = splitting data across multiple databases**
**Consistent Hashing = intelligently distributing data so adding/removing servers causes minimal movement of data**



The confusion happens because the ring drawing is symbolic. Let me explain with **actual numbers**.

Suppose our hash ring is from **0 → 100**.

Initially we have:

```text
DB1 = 20
DB2 = 40
DB3 = 60
DB4 = 80
```

The ring becomes:

```text
          0/100
             |
             |
DB4(80)      |      DB1(20)
      \       |      /
       \      |     /
        \     |    /
         DB3(60)--DB2(40)
```

Now assume users/data are hashed:

```text
UserA = 10
UserB = 30
UserC = 35
UserD = 50
UserE = 75
```

Rule:

**Move clockwise until you find the first DB.**

So:

```text
UserA (10) → DB1(20)

UserB (30) → DB2(40)

UserC (35) → DB2(40)

UserD (50) → DB3(60)

UserE (75) → DB4(80)
```

Current mapping:

```text
DB1 → UserA

DB2 → UserB, UserC

DB3 → UserD

DB4 → UserE
```

---

Now we add a new server:

```text
DB5 = 32
```

Updated ring:

```text
          0/100
             |
             |
DB4(80)      |      DB1(20)
      \       |      /
       \      |     /
        DB3(60) DB2(40)
               /
           DB5(32)
```

Now check users again:

```text
UserA = 10 → DB1
UserB = 30 → DB5
UserC = 35 → DB2
UserD = 50 → DB3
UserE = 75 → DB4
```

Notice what changed:

Before:

```text
UserB → DB2
```

After:

```text
UserB → DB5
```

Only that small range moved.

Everything else:

```text
UserA → same
UserC → same
UserD → same
UserE → same
```

So only keys between:

```text
DB1(20) ----- DB5(32)
```

move to the new server.

That is why people say:

```text
Old → DB2
New → DB5
```

Only nearby keys move; **not all data**.

Compare with normal hashing:

```text
Shard = UserID % NumberOfServers
```

Adding a server:

```text
4 servers → User123 % 4

5 servers → User123 % 5
```

Almost every key changes.

With consistent hashing:

```text
Only a small portion moves
```

That is the main advantage interviewers expect you to say.


You are asking for the **problems and limitations of Database Sharding and Consistent Hashing**. In HLD interviews, both are commonly discussed.

# Problems of Database Sharding

Although sharding improves scalability, it introduces several challenges.

## 1. Complex Queries

Suppose users are distributed like:

```text
Shard1 → User1–1000
Shard2 → User1001–2000
Shard3 → User2001–3000
```

Now query:

```sql
SELECT * FROM users WHERE age > 25
```

Problem:

The query must run on **all shards**.

```text
Query
   ↓
Shard1
Shard2
Shard3
   ↓
Merge results
```

Issues:

* Slower execution
* Higher network cost

---

## 2. Cross-shard JOIN becomes difficult

Example:

```text
User Data → Shard1
Order Data → Shard3
```

Query:

```sql
SELECT *
FROM users u
JOIN orders o
ON u.id=o.user_id
```

Problem:

Data exists on different servers.

Result:

❌ Expensive joins
❌ Slow processing

---

## 3. Uneven Distribution (Hotspot Problem)

Example:

```text
Shard1 → 80% traffic
Shard2 → 10%
Shard3 → 10%
```

One shard becomes overloaded.

Effects:

* Slow response
* High CPU usage
* Bottleneck

---

## 4. Resharding Complexity

Suppose:

```text
3 shards
```

Traffic increases:

```text
Need 5 shards
```

Moving data:

```text
Shard1 → Shard4
Shard2 → Shard5
```

Problems:

* Data migration
* Downtime risk
* Large transfer cost

---

## 5. Transactions become difficult

Single database transaction:

```sql
BEGIN
Update accountA
Update accountB
COMMIT
```

Across shards:

```text
AccountA → Shard1
AccountB → Shard2
```

Problem:

Need distributed transactions.

Issues:

* Complex implementation
* Performance overhead

---

# Problems of Consistent Hashing

Consistent hashing solves data movement problems, but it also has limitations.

## 1. Uneven data distribution

Without virtual nodes:

```text
Ring:

DB1 ------- DB2 ---- DB3
```

Problem:

```text
DB2 → 70% data
DB1 → 15%
DB3 → 15%
```

Some servers become overloaded.

Solution:

Use **virtual nodes**.

---

## 2. Hot Keys Problem

Suppose one key becomes extremely popular:

```text
User123 profile
```

Millions of requests:

```text
User123 → DB2
```

Problem:

Only one server receives huge traffic.

Effects:

* Server overload
* Increased latency

---

## 3. Data migration still exists

People often think:

```text
Consistent hashing = zero movement
```

Wrong.

It actually means:

```text
Less movement
```

Example:

```text
Add DB5
```

Still:

```text
Some keys move to DB5
```

---

## 4. Extra implementation complexity

Need to manage:

* Hash ring
* Virtual nodes
* Node failures
* Routing logic

Compared to:

```text
UserID % N
```

consistent hashing is more complex.

---

# Interview Summary Table

| Problem                   | Sharding |       Consistent Hashing |
| ------------------------- | -------: | -----------------------: |
| Complex queries           |        ✅ |                        ❌ |
| Cross-shard joins         |        ✅ |                        ❌ |
| Distributed transactions  |        ✅ |                        ❌ |
| Uneven load               |        ✅ |                        ✅ |
| Hotspots                  |        ✅ |                        ✅ |
| Data movement             |        ✅ | Reduced but still exists |
| Implementation complexity |   Medium |                     High |

One-line interview summary:

**Sharding improves scalability but complicates queries and transactions. Consistent hashing reduces data movement but needs virtual nodes and careful load balancing.**




**Virtual Nodes (VNodes)** are an important concept in **consistent hashing**. They solve the problem of **uneven data distribution**.

# Problem without Virtual Nodes

Suppose we have 3 database servers:

```text
DB1
DB2
DB3
```

Hash ring:

```text
           DB1
          /   \
         /     \
      DB3      DB2
```

Now data keys are placed on the ring.

Example:

```text
DB1 → 15% data

DB2 → 70% data

DB3 → 15% data
```

Problem:

```text
DB2 overloaded ❌
DB1 underused ❌
DB3 underused ❌
```

Load is not balanced.

---

# Solution: Virtual Nodes

Instead of putting **one position** for each server on the ring, create **multiple virtual copies** of the server.

Example:

Real servers:

```text
DB1
DB2
DB3
```

Virtual nodes:

```text
DB1-A
DB1-B
DB1-C

DB2-A
DB2-B
DB2-C

DB3-A
DB3-B
DB3-C
```

Now place them randomly around the ring.

```text
DB1-A → DB2-A → DB3-A → DB1-B
      → DB2-B → DB3-B → DB1-C
      → DB2-C → DB3-C
```

Hash ring:

```text
                DB1-A
             /         \
      DB3-C              DB2-A
        /                  \
   DB2-C                    DB3-A
      \                    /
       DB1-B -------- DB2-B
             \      /
               DB3-B
```

---

# How data gets stored

Suppose:

```text
UserA = hash(25)
UserB = hash(43)
UserC = hash(70)
```

Move clockwise:

```text
UserA → DB2-A

UserB → DB3-B

UserC → DB1-C
```

Even though requests hit different virtual nodes:

```text
DB2-A → Real DB2
DB3-B → Real DB3
DB1-C → Real DB1
```

Data still goes to the real database.

---

# Benefits of Virtual Nodes

### 1. Better load balancing

Without VNodes:

```text
DB2 → 70%
```

With VNodes:

```text
DB1 → 34%

DB2 → 33%

DB3 → 33%
```

Balanced distribution.

---

### 2. Easier server addition

Suppose new server:

```text
DB4
```

Add:

```text
DB4-A
DB4-B
DB4-C
```

Only nearby data moves.

---

### 3. Easier server removal

If:

```text
DB2 fails
```

Remove:

```text
DB2-A
DB2-B
DB2-C
```

Only nearby keys are redistributed.

---

# Interview one-line answer

**Virtual nodes are multiple logical copies of a physical server placed on a consistent hash ring to achieve better load balancing and reduce hotspots.**



You are mixing **virtual nodes** with **replicas**. They are different concepts.

* **Virtual Nodes (VNodes)** → for **load balancing in consistent hashing**
* **Replicas** → for **data redundancy and fault tolerance**

Virtual nodes **do not store separate copies of data**. They are only **logical entries on the hash ring**.

Let's clear it up.

# Virtual node flow

Suppose:

```text
Real DB servers:

DB1
DB2
DB3
```

Virtual nodes:

```text
DB1-A
DB1-B
DB1-C

DB2-A
DB2-B
DB2-C
```

Request:

```text
User123
   ↓
Hash(User123)
   ↓
DB2-B
```

Now:

```text
DB2-B
```

is **not another database**.

It simply means:

```text
DB2-B → Actual physical DB2
```

So the write goes directly to:

```text
User123
   ↓
Hash Ring
   ↓
DB2-B
   ↓
Real DB2
```

No synchronization is needed between DB2-A and DB2-B because:

```text
DB2-A
DB2-B
DB2-C
```

all point to the **same physical machine (DB2)**.

---

Now let's talk about **replicas**, because that is where synchronization happens.

Suppose:

```text
DB2 (Primary)
      |
      +------ Replica1
      |
      +------ Replica2
```

Write flow:

```text
Client
   ↓
Primary DB2
   ↓
Replication
   ↓
Replica1
Replica2
```

The client normally writes only to the **Primary**.

Example:

```text
INSERT User123
```

Flow:

```text
Client
   ↓
Primary DB2
   ↓
Update Replica1
   ↓
Update Replica2
```

---

Two synchronization methods exist:

### 1. Synchronous replication

```text
Client
   ↓
Primary DB
   ↓
Replica1 updated
   ↓
Replica2 updated
   ↓
Success response
```

Advantages:

✅ Strong consistency

Disadvantages:

❌ Slower writes

---

### 2. Asynchronous replication

```text
Client
   ↓
Primary DB
   ↓
Success immediately
   ↓
Replica update later
```

Advantages:

✅ Fast writes

Disadvantages:

❌ Temporary inconsistency

Example:

```text
Write data
      ↓
Immediately read from Replica
      ↓
Old data may appear
```

This is called:

**Eventual consistency**

---

# Final distinction (important for interviews)

| Concept            |                     Purpose |
| ------------------ | --------------------------: |
| Sharding           | Split data across databases |
| Consistent Hashing |      Decide where data goes |
| Virtual Nodes      |    Better load distribution |
| Replication        |         Keep copies of data |
| Replica Sync       |     Update copied databases |

The key misunderstanding was:

**Virtual nodes are not replicas. They are just multiple logical positions of the same database on the hash ring.**


