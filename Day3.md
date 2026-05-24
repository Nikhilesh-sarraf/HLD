# CAP Theorem in Distributed Systems

The CAP theorem says that in a distributed database system, you can only fully guarantee **two out of three properties** at the same time:

- Consistency (C)
- Availability (A)
- Partition Tolerance (P)

---

# 1. Consistency (C)

Every node in the system shows the same data at the same time.

If you write something, all users immediately see the latest version.

Example:

```text
User updates profile picture
↓
All servers instantly show new picture
```

---

# 2. Availability (A)

The system always responds to requests (even if the data is not the latest).

No request should fail.

Example:

```text
User refreshes Instagram feed
↓
System always returns some response
(even if feed is slightly old)
```

---

# 3. Partition Tolerance (P)

The system continues to work even if there is a network failure between nodes.

Example:

```text
Server A  ❌ network failure ❌  Server B
```

Even if servers cannot communicate, the distributed system should continue running.

---

# Core Idea of CAP Theorem

When a network partition happens (P is required in real systems), you must choose between:

- Consistency (C)
- Availability (A)

You cannot guarantee both perfectly during a partition.

---

# CP System (Consistency + Partition Tolerance)

A CP system prioritizes:

- Correct data
- Partition handling

But may sacrifice availability.

## Properties

✔ Always correct/latest data  
✔ Handles network partition  
❌ Some requests may fail or wait  

## Example

- Banking systems
- HBase
- MongoDB (depending on configuration)

---

# AP System (Availability + Partition Tolerance)

An AP system prioritizes:

- Always responding
- Partition handling

But data may become temporarily inconsistent.

## Properties

✔ System always responds  
✔ Handles network partition  
❌ Data may be slightly outdated  

## Example

- Cassandra
- Social media feeds
- WhatsApp message delivery systems

---

# CA System (Consistency + Availability)

A CA system provides:

- Consistency
- Availability

But only if there is **no partition**.

In real distributed systems, partitions are unavoidable.

So pure CA systems are not realistic at large scale.

---

# Real-World Examples

| System | CAP Type |
|---|---|
| Cassandra | AP |
| HBase | CP |
| MongoDB | Can lean CP |
| Banking systems | CP |
| Social media feeds | AP |

---

# Example 1: WhatsApp / Instagram (AP System)

Imagine you send a message on WhatsApp.

## During a Network Problem

```text
Your phone → Server A
Friend phone → Server B

Server A ❌ cannot sync ❌ Server B
```

System chooses:

- Availability
- Partition tolerance

## Result

✔ You can still send messages  
✔ App keeps working  
❌ Friend may receive message later  

This is called:

> Eventual Consistency

Meaning:

Data becomes consistent after some time.

---

# Example 2: Bank System (CP System)

Imagine transferring ₹1000 to someone.

## During Network Failure

Servers cannot communicate.

System chooses:

- Consistency
- Partition tolerance

## Result

✔ Account balance always remains correct  
✔ No incorrect money transfer  
❌ Transaction may fail temporarily  
❌ You may see:

```text
Service unavailable
```

Banking systems prefer correctness over availability.

---

# CAP Theorem Intuition ("Proof")

## Step 1: Assume We Want All Three

Suppose we try to build a system with:

- Consistency
- Availability
- Partition tolerance

So even if network breaks:

- System still works
- Every request gets response
- All nodes always show same data

---

## Step 2: Introduce Network Partition

Imagine two servers:

```text
Node A  <---X--- network failure ---X--->  Node B
```

Now the nodes cannot communicate.

Partition has happened.

---

## Step 3: User Writes Data

User writes:

```text
x = 10
```

But request only reaches Node A.

Now:

```text
Node A = 10
Node B = 5
```

---

## Step 4: Test the Three Conditions

# Condition 1: Consistency (C)

Consistency requires:

```text
All nodes must show same value
```

But:

```text
Node A = 10
Node B = 5
```

❌ Consistency broken

To fix consistency, nodes must sync.

But network is broken.

---

# Condition 2: Availability (A)

Availability requires:

```text
Every request must receive response
```

So Node B must also respond.

But Node B has stale data.

If it responds:

❌ Data may be incorrect

---

# Condition 3: Partition Tolerance (P)

Partition tolerance means:

```text
System continues operating despite network failure
```

So system cannot simply shut down forever.

---

# Key Understanding

## Availability Means

```text
System always responds
```

NOT:

```text
System always returns latest data
```

So:

✔ Response success guaranteed  
❌ Correctness not guaranteed  

---

## Consistency Means

```text
Every read gets latest written value
```

So:

✔ Data correctness guaranteed  
❌ Some requests may wait or fail  

---

# Instagram Example Explained Properly

During partition:

Instagram must choose:

## Option 1: Availability

```text
Show slightly old feed
```

✔ App works  
❌ Feed may be stale  

---

## Option 2: Consistency

```text
Wait until servers sync
```

✔ Feed always latest  
❌ Refresh may fail or hang  

---

# System Availability Percentages

This is different from CAP theorem.

Availability percentage means:

> How often system stays operational over time

---

# Total Time in a Year

```text
365 days
= 8760 hours
= 525,600 minutes
```

---

# Downtime Formula

```math
Downtime = (1 - availability) \times total\ time
```

---

# 99% Availability

```math
1\% \ downtime
```

```math
0.01 \times 365 \approx 3.65\ days
```

## Meaning

System can be down around:

```text
3–4 days/year
```

---

# 99.9% Availability ("Three Nines")

```math
0.1\% \ downtime
```

```math
0.001 \times 365 \approx 0.365\ days
```

≈ 8.77 hours/year

## Meaning

Few hours downtime per year.

Large companies usually target at least this.

---

# 99.99% Availability ("Four Nines")

≈ 52 minutes downtime/year

Used in:

- Cloud systems
- Payment systems

---

# 99.999% Availability ("Five Nines")

≈ 5 minutes downtime/year

Used in:

- Google-scale infrastructure
- Amazon-scale infrastructure
- Critical enterprise systems

---

# Important Difference

| Concept | Focus | Main Question |
|---|---|---|
| CAP theorem | Behavior during partition | What do we sacrifice: C or A? |
| 99.9% uptime | Reliability over time | How often is system available? |

---

# CAP Theorem Is NOT About Uptime

✔ Truth:

CAP theorem is **not** about uptime percentage.

CAP theorem is about:

```text
What happens WHEN network breaks?
```

You must choose between:

- Consistency
- Availability

---

# Final Key Insight

Even a system with:

```text
99.999% uptime
```

still faces network partitions.

So CAP theorem still applies.

---

# One-Line Memory Trick

```text
CAP = During partition, choose Consistency or Availability
```

```text
Uptime % = How often system stays operational
```
