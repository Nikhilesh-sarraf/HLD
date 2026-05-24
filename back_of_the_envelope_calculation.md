Back-of-the-envelope calculation (BOE) in System Design means doing **quick approximate calculations** to estimate the scale of a system before designing it. It helps answer questions like:

* How many users are there?
* How much storage is needed?
* How many requests per second (RPS)?
* How much bandwidth is required?
* How many servers might be needed?

Instead of exact values, we use **rounded assumptions** because system design interviews care more about reasoning than precision.

# Why it is important

Suppose someone asks:

**"Design YouTube"**

Before drawing architecture, you need to know:

* 1 million users or 1 billion users?
* Videos uploaded per day?
* Traffic size?
* Storage requirement?

Without scale estimation, your architecture may become unrealistic.

---

# Common assumptions used in interviews

People memorize some standard values:

| Quantity             |     Approximate value |
| -------------------- | --------------------: |
| 1 KB                 |             10³ bytes |
| 1 MB                 |             10⁶ bytes |
| 1 GB                 |             10⁹ bytes |
| 1 TB                 |            10¹² bytes |
| 1 day                |            86,400 sec |
| Read:Write ratio     | usually 100:1 or 10:1 |
| Peak traffic         |         ~2–5× average |
| Average text message |             100 bytes |
| Average image        |           200 KB–2 MB |
| Average video        |            MBs to GBs |

---

# Main calculations used

## 1. Request Per Second (RPS)

Formula:

[
RPS=\frac{\text{Total requests per day}}{86400}
]

Example:

10 million requests/day

[
RPS=\frac{10,000,000}{86400}
]

≈ 115 requests/sec

Peak traffic:

[
Peak\ RPS=115\times5
]

≈ 575 RPS

---

## 2. Storage calculation

Formula:

[
Storage = Users \times Data\ per\ user
]

Example:

20 million users

Each user uploads 5 MB/day

Daily storage:

[
20M \times 5MB
]

=100 TB/day

Yearly:

[
100 \times 365
]

≈36.5 PB/year

---

## 3. Bandwidth calculation

Formula:

[
Bandwidth=RPS\times Response\ size
]

Example:

1000 requests/sec

Response size = 500 KB

[
1000\times500KB
]

=500 MB/s

---

## 4. Cache estimation

Formula:

[
Cache = Hot\ data\ percentage\times Total\ data
]

Example:

Total data = 20 TB

20% frequently accessed

[
20TB\times0.2
]

=4 TB cache

---

# Complete interview example

Question:

**Design WhatsApp**

### Step 1: Users

Assume:

* 500 million users
* 20% daily active

Daily active users:

[
500M\times0.2
]

=100M users

---

### Step 2: Messages

Assume each user sends:

50 messages/day

Total messages:

[
100M\times50
]

=5 billion/day

---

### Step 3: RPS

[
\frac{5B}{86400}
]

≈57,870 msg/sec

Peak:

[
57,870\times5
]

≈290K msg/sec

---

### Step 4: Storage

Message size:

100 bytes

Daily storage:

[
5B\times100
]

=500 GB/day

Yearly:

[
500\times365
]

≈182 TB/year

---

# Interview process (important)

In interviews, follow this order:

**1. Clarify requirements**

* Functional requirements
* Non-functional requirements

**2. Estimate scale**

* Users
* DAU
* RPS
* Storage
* Bandwidth

**3. Design high-level architecture**

* Load balancer
* Application servers
* Database
* Cache
* Queue

**4. Discuss bottlenecks**

* Database scaling
* Caching
* Replication
* Sharding

---



Good question. I skipped the **unit conversion step**, which is very important in back-of-the-envelope calculations.

Let's break it down carefully.

Given:

* Total messages/day = **5 billion**
* Size of each message = **100 bytes**

Daily storage:

[
5B \times 100
]

Substitute:

[
5 \times 10^9 \times 100
]

Since:

[
100 = 10^2
]

So:

[
5 \times 10^9 \times 10^2
]

[
=5 \times 10^{11}\ bytes
]

Now convert bytes → GB.

In BOE calculations we approximate:

[
1GB \approx 10^9\ bytes
]

Therefore:

[
\frac{5\times10^{11}}{10^9}
]

[
=5\times10^2
]

[
=500GB
]

So:

**Daily storage ≈ 500 GB/day**

Now yearly storage:

[
500\times365
]

[
=182,500GB
]

Convert GB → TB:

[
1TB\approx1000GB
]

[
\frac{182,500}{1000}
]

[
=182.5TB
]

So:

**Yearly storage ≈ 182 TB/year**

The flow you should memorize for interviews:

**Messages × Size/message → Total bytes → Convert to GB/TB/PB**

For example:

* (10^6) → MB
* (10^9) → GB
* (10^{12}) → TB
* (10^{15}) → PB

That shortcut is why interviewers can quickly say:

> "5 billion × 100 bytes → roughly 500 GB/day"

without writing every conversion step.


This is a very useful cheat sheet for **System Design Back-of-the-Envelope calculations**. Try to memorize it.

# Data size units (Bytes)

| Unit           |    Value | Power of 10 |
| -------------- | -------: | ----------: |
| Byte (B)       |        1 |      (10^0) |
| Kilobyte (KB)  |  1,000 B |      (10^3) |
| Megabyte (MB)  | 1,000 KB |      (10^6) |
| Gigabyte (GB)  | 1,000 MB |      (10^9) |
| Terabyte (TB)  | 1,000 GB |   (10^{12}) |
| Petabyte (PB)  | 1,000 TB |   (10^{15}) |
| Exabyte (EB)   | 1,000 PB |   (10^{18}) |
| Zettabyte (ZB) | 1,000 EB |   (10^{21}) |

**Interview approximation:**

* KB → (10^3)
* MB → (10^6)
* GB → (10^9)
* TB → (10^{12})
* PB → (10^{15})

*(Real computers often use 1024, but in system design interviews use 1000 for quick estimates.)*

---

# Number scale table

| Name        |                Number | Power of 10 |
| ----------- | --------------------: | ----------: |
| Thousand    |                 1,000 |      (10^3) |
| Million     |             1,000,000 |      (10^6) |
| Billion     |         1,000,000,000 |      (10^9) |
| Trillion    |     1,000,000,000,000 |   (10^{12}) |
| Quadrillion | 1,000,000,000,000,000 |   (10^{15}) |

---

# Quick memory trick

Notice the pattern:

| Name        | Power |
| ----------- | ----: |
| Thousand    |    +3 |
| Million     |    +6 |
| Billion     |    +9 |
| Trillion    |   +12 |
| Quadrillion |   +15 |

Every step increases by **3 powers**.

---

# Useful interview shortcuts

| Value             | Shortcut     |
| ----------------- | ------------ |
| 1 Million users   | (10^6) users |
| 100 Million users | (10^8) users |
| 1 Billion users   | (10^9) users |
| 1M requests/day   | ≈ 12 RPS     |
| 10M requests/day  | ≈ 115 RPS    |
| 100M requests/day | ≈ 1157 RPS   |
| 1B requests/day   | ≈ 11.5K RPS  |

---

Example:

If a system has:

* 100M users = (10^8)
* Each uploads 10MB = (10^7) bytes

Storage:

[
10^8 \times 10^7
]

[
=10^{15}
]

[
=1PB
]

This is exactly how experienced system design candidates do quick calculations mentally.



