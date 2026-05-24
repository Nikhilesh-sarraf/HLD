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


For system design interviews, a very common sequence to memorize is:

**Users → DAU → Requests → RPS → Storage → Bandwidth → Cache → Servers**

