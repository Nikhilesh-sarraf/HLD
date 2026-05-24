# Sharding in HLD (High-Level Design)

Sharding is one of the most important concepts in scalable system design.

It is mainly used in databases when a single database becomes too large or too slow to handle all traffic.

---

# 1. What is Sharding?

Sharding means:

> Splitting a large database into multiple smaller databases.

Each smaller database is called a **Shard**.

Instead of storing all users/data in one database server:

```text
One Huge Database ❌
```

We divide data:

```text
Shard 1
Shard 2
Shard 3
Shard 4
```

This helps distribute:

* Storage
* Read traffic
* Write traffic
* CPU load

---

# 2. Why Do We Need Sharding?

Imagine:

A social media app has:

* 500 million users
* billions of messages
* millions of requests/sec

One DB server cannot handle:

* Too much data
* Too many queries
* High traffic

Problems:

* Slow queries
* High latency
* DB crashes
* Storage full

So we divide data across multiple servers.

---

# 3. Simple Example

Suppose we have users:

```text
User 1
User 2
User 3
...
User 1 Crore
```

Instead of storing all in one DB:

We split users.

Example:

| Shard   | Users                  |
| ------- | ---------------------- |
| Shard 1 | User ID 1–250000       |
| Shard 2 | User ID 250001–500000  |
| Shard 3 | User ID 500001–750000  |
| Shard 4 | User ID 750001–1000000 |

Now load is distributed.

---

# 4. Architecture of Sharding

```text
Client
   |
Load Balancer
   |
Application Server
   |
Shard Router / Sharding Logic
   |
 -------------------------
 |     |      |         |
DB1   DB2    DB3      DB4
```

The application decides:

```text
Which shard contains the data?
```

---

# 5. Types of Sharding

---

## A. Range-Based Sharding

Data divided by ranges.

Example:

| Shard | User IDs  |
| ----- | --------- |
| DB1   | 1–1000    |
| DB2   | 1001–2000 |
| DB3   | 2001–3000 |

### Advantages

* Easy to understand
* Easy querying for ranges

### Disadvantages

Hotspot problem:

If all new users go to last shard:

```text
DB3 overloaded
```

---

## B. Hash-Based Sharding

Most common.

We use a hash function:

```text
shard = userID % number_of_shards
```

Example:

shard = userID \bmod N

If:

```text
N = 4
```

Then:

| User ID | Shard |
| ------- | ----- |
| 10      | 2     |
| 15      | 3     |
| 21      | 1     |

### Advantages

* Good load balancing
* Even distribution

### Disadvantages

Hard to reshard later.

---

## C. Directory-Based Sharding

A lookup table stores mapping.

Example:

| User ID | DB  |
| ------- | --- |
| 101     | DB2 |
| 102     | DB4 |

### Advantages

* Flexible
* Easy migration

### Disadvantages

* Extra lookup needed
* Directory DB can become bottleneck

---

# 6. Horizontal vs Vertical Partitioning

## Horizontal Partitioning (Sharding)

Rows are divided.

Example:

```text
DB1 -> Users 1–1000
DB2 -> Users 1001–2000
```

---

## Vertical Partitioning

Columns are divided.

Example:

```text
DB1 -> user_id, name
DB2 -> photos, videos
```

---

# 7. Real-World Example

## Instagram

* User data spread across many shards
* Media metadata sharded
* Messages sharded

Because:

Millions of uploads happen every minute.

---

## WhatsApp

Messages distributed across servers.

One server cannot store all chats.

---

## Amazon

Orders database heavily partitioned/sharded.

---

# 8. Challenges in Sharding

Sharding solves scaling problems but introduces complexity.

---

## A. Resharding Problem

Suppose:

```text
4 shards → now traffic increased
Need 8 shards
```

Data migration becomes difficult.

Hash mappings change.

---

## B. Joins Become Difficult

If user data is in DB1 and order data in DB3:

```sql
JOIN
```

becomes expensive.

Cross-shard joins are hard.

---

## C. Uneven Distribution

Some shards may get more traffic.

Example:

Celebrities generate huge load.

This is called:

```text
Hot Shard Problem
```

---

## D. Transactions Become Hard

Transactions across multiple shards are complex.

Example:

Money transfer between accounts in different shards.

---

# 9. Consistent Hashing (Advanced Concept)

Used to solve resharding problem.

Instead of simple modulo:

```text
userID % N
```

we use a hash ring.

Benefits:

* Minimal data movement
* Easy scaling
* Used in distributed systems

Systems using this:

* Cassandra
* DynamoDB
* Redis Cluster

---

# 10. Sharding vs Replication

Students confuse these a lot.

| Sharding                  | Replication            |
| ------------------------- | ---------------------- |
| Splits data               | Copies data            |
| Used for scaling writes   | Used for scaling reads |
| Different data in each DB | Same data in all DBs   |

---

## Example

### Replication

```text
Primary DB
   |
------------
|     |     |
R1    R2    R3
```

Same data everywhere.

---

### Sharding

```text
DB1 -> User 1–1000
DB2 -> User 1001–2000
DB3 -> User 2001–3000
```

Different data.

---

# 11. When to Use Sharding?

Use when:

✅ Database too large
✅ Write traffic too high
✅ Single DB bottleneck
✅ Horizontal scaling needed

Do NOT use early.

Because sharding increases:

* Complexity
* Maintenance
* Query difficulty

---

# 12. Interview Explanation (Short Version)

If interviewer asks:

## “What is sharding?”

You can say:

> Sharding is a database scaling technique where large datasets are horizontally partitioned across multiple database servers called shards. Each shard stores only a subset of the total data, helping distribute storage and traffic load, improving scalability and performance.

---

# 13. Example Interview Answer

## “How does Instagram scale databases?”

You can say:

* Instagram shards user data
* Users distributed across multiple DB servers
* Requests routed using shard key
* Replication used for read scaling
* Caching added for hot data

---

# 14. Important Terms

| Term               | Meaning                         |
| ------------------ | ------------------------------- |
| Shard              | Small partitioned DB            |
| Shard Key          | Key deciding shard              |
| Resharding         | Adding/removing shards          |
| Hotspot            | Overloaded shard                |
| Consistent Hashing | Advanced shard distribution     |
| Cross-Shard Query  | Query involving multiple shards |

---

# 15. Final Intuition

Think like this:

Without sharding:

```text
One shop serving entire city ❌
```

With sharding:

```text
Many small shops across city ✅
```

Load gets distributed.

Scalability improves massively.
