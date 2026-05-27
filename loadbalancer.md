A **Load Balancer (LB)** is a component that **distributes incoming requests across multiple servers** so that no single server gets overloaded.

In HLD, load balancers are used to improve:

* Scalability
* Availability
* Fault tolerance
* Performance

# Without Load Balancer

Suppose 10,000 users send requests.

```text
Users
   ↓
Application Server
```

Problem:

```text
Single Server
     ↓
CPU = 100%
Memory full
     ↓
Crash ❌
```

If the server fails:

```text
Website Down ❌
```

---

# With Load Balancer

```text
               Users
                  ↓
        +----------------+
        | Load Balancer  |
        +----------------+
          /      |      \
         /       |       \
        ↓        ↓        ↓

    Server1  Server2  Server3
```

Request distribution:

```text
Request1 → Server1
Request2 → Server2
Request3 → Server3
```

Benefits:

✅ Better performance
✅ High availability
✅ No server overload

---

# How a Load Balancer Works

Step 1:

```text
Client sends request
```

Step 2:

```text
Load Balancer receives request
```

Step 3:

```text
LB chooses a server
```

Step 4:

```text
Request forwarded
```

---

# Load Balancing Algorithms

## 1. Round Robin

Requests are distributed sequentially.

```text
Request1 → Server1
Request2 → Server2
Request3 → Server3
Request4 → Server1
Request5 → Server2
```

Advantages:

✅ Simple

Problem:

❌ Assumes all servers have equal capacity

---

## 2. Weighted Round Robin

Some servers receive more traffic.

Example:

```text
Server1 weight = 3
Server2 weight = 2
Server3 weight = 1
```

Distribution:

```text
S1 S1 S1 S2 S2 S3
```

Useful when:

```text
Server1 = powerful machine
Server3 = weaker machine
```

---

## 3. Least Connections

Send request to the server with fewer active connections.

Example:

```text
Server1 → 100 connections

Server2 → 20 connections

Server3 → 50 connections
```

Next request:

```text
→ Server2
```

Advantages:

✅ Better for uneven traffic

---

## 4. IP Hashing

Request routing based on user IP.

```text
Hash(UserIP)
      ↓
Specific Server
```

Advantages:

✅ Same user reaches same server

Useful for:

* Sessions
* User-specific data

---

# Health Checks

Load balancer periodically checks servers.

```text
LB
 ↓
Server1 → Healthy ✅
Server2 → Healthy ✅
Server3 → Failed ❌
```

Traffic becomes:

```text
LB
 ↓
Server1
Server2
```

No requests go to Server3.

---

# Types of Load Balancers

## Layer 4 Load Balancer

Works at:

```text
Transport Layer
```

Uses:

* IP
* TCP
* UDP

Fast but less intelligent.

---

## Layer 7 Load Balancer

Works at:

```text
Application Layer
```

Uses:

* URL
* Headers
* Cookies

Example:

```text
/api/users → User Service

/api/payment → Payment Service
```

---

# HLD Example

Large e-commerce website:

```text
Users
   ↓
Load Balancer
   ↓
+----------------+
| App Server 1   |
| App Server 2   |
| App Server 3   |
+----------------+
   ↓
Database
```

---

# Limitations / Problems

1. Single point of failure

```text
Users
   ↓
Load Balancer ❌
```

Solution:

Use multiple load balancers.

```text
Users
   ↓
LB1     LB2
```

2. Extra cost

3. Configuration complexity

4. Session management issues

---

Interview one-line answer:

**A Load Balancer distributes traffic across multiple servers to improve scalability, availability, and fault tolerance.**



Dynamic load balancing means the load balancer **makes decisions based on the current state of servers** (CPU usage, active connections, response time, traffic, etc.) instead of using a fixed rule.

Static methods:

```text
Round Robin
Weighted Round Robin
```

Dynamic methods:

```text
Least Connections
Least Response Time
Resource-based
Adaptive Load Balancing
```

# Types of Dynamic Load Balancing

## 1. Least Connections

The request goes to the server with the fewest active connections.

Example:

```text
Server1 → 100 users
Server2 → 20 users
Server3 → 50 users
```

Next request:

```text
→ Server2
```

Because it currently has less load.

Advantages:

✅ Good for uneven traffic
✅ Simple and efficient

Disadvantages:

❌ Doesn't consider server capacity

---

## 2. Weighted Least Connections

Combines:

* Server capacity
* Active connections

Example:

```text
Server1:
Connections = 40
Weight = 5

Server2:
Connections = 20
Weight = 2
```

Decision:

```text
Load = Connections / Weight
```

Lower load gets selected.

Advantages:

✅ Better when servers have different power

---

## 3. Least Response Time

Choose the server with the fastest response.

Example:

```text
Server1 → 120 ms

Server2 → 70 ms

Server3 → 200 ms
```

Next request:

```text
→ Server2
```

Advantages:

✅ Better user experience

Disadvantages:

❌ Needs continuous monitoring

---

## 4. Resource-Based Load Balancing

The load balancer checks system resources.

Example:

```text
Server1:
CPU = 90%
RAM = 85%

Server2:
CPU = 30%
RAM = 40%
```

Next request:

```text
→ Server2
```

Advantages:

✅ Uses actual machine health

Disadvantages:

❌ Requires monitoring agents

---

## 5. Adaptive Load Balancing

Uses multiple factors:

* CPU usage
* Memory usage
* Response time
* Active connections
* Network latency

Example:

```text
Score =
CPU + Memory + Active Connections + Latency
```

Request goes to the server with the best score.

Advantages:

✅ Intelligent routing

Disadvantages:

❌ More complex

---

# Comparison Table

| Dynamic Type               |                        Uses |
| -------------------------- | --------------------------: |
| Least Connections          |                Active users |
| Weighted Least Connections | Active users + server power |
| Least Response Time        |              Fastest server |
| Resource-Based             |               CPU/RAM usage |
| Adaptive                   |            Multiple metrics |

---

Typical HLD interview answer:

```text
Static LB:
Request → Server1 → Server2 → Server3

Dynamic LB:
Check current load
        ↓
Choose best server
```

One-line summary:

**Dynamic load balancing distributes requests using real-time server conditions instead of fixed rules.**




These are **types of load balancers** commonly used in HLD. They differ based on **where they operate and how they route traffic**.

# Overall hierarchy

```text id="4eu3ol"
Load Balancers
      |
      +---- Network Layer LB (L4)
      |
      +---- Application Layer LB (L7)
      |
      +---- DNS Load Balancer
      |
      +---- Global Server Load Balancer (GSLB)
```

---

# 1. Network Layer Load Balancer (Layer 4 LB)

Works at:

```text id="rqotwx"
OSI Layer 4
(TCP / UDP)
```

Uses:

* Source IP
* Destination IP
* Port number

It does **not inspect application data**.

Flow:

```text id="7ek9rk"
Client
   ↓
L4 Load Balancer
   ↓
Server1
Server2
Server3
```

Example:

```text id="pb5ax8"
192.168.1.10:8080
```

Routing decision based on:

```text id="b4hy3u"
IP + Port
```

Advantages:

✅ Very fast
✅ Low overhead
✅ High throughput

Disadvantages:

❌ Cannot inspect URLs or headers

Use cases:

* Gaming servers
* TCP applications
* High-speed systems

---

# 2. Application Layer Load Balancer (Layer 7 LB)

Works at:

```text id="i0fx9j"
OSI Layer 7
(HTTP / HTTPS)
```

Can inspect:

* URL
* Headers
* Cookies
* Request body

Example:

```text id="z4zzqw"
/api/users
/api/payments
/images
```

Flow:

```text id="zv0w6x"
Client
   ↓
L7 Load Balancer
      |
      +---- /users → User Service
      |
      +---- /payments → Payment Service
      |
      +---- /images → Image Service
```

Advantages:

✅ Smart routing
✅ SSL termination
✅ API routing

Disadvantages:

❌ Slower than L4
❌ More CPU usage

Use cases:

* Microservices
* Web applications
* API gateways

---

# 3. DNS Load Balancer

DNS itself distributes users among multiple IP addresses.

Example:

```text id="d8x2b8"
example.com
```

DNS returns:

```text id="x5k8bx"
192.168.1.10
192.168.1.11
192.168.1.12
```

Flow:

```text id="v0d86d"
User
   ↓
DNS
   ↓
Choose IP
   ↓
Server
```

Advantages:

✅ Very simple
✅ Geographic routing possible

Disadvantages:

❌ DNS caching problem

Example:

```text id="5m2s9p"
User cached old IP
```

If a server fails:

```text id="j4ph73"
User may still hit failed server ❌
```

Use cases:

* Basic traffic distribution
* Multi-region systems

---

# 4. Global Server Load Balancer (GSLB)

Used when servers exist in **multiple regions or countries**.

Example:

```text id="nckp9f"
USA Server
India Server
Europe Server
Singapore Server
```

Flow:

```text id="ffhf7z"
User
   ↓
GSLB
   ↓
Nearest Region
   ↓
Regional Load Balancer
   ↓
Application Servers
```

Example:

```text id="o7uvgh"
User from Nepal
      ↓
Singapore server

User from USA
      ↓
USA server
```

Routing factors:

* Geography
* Latency
* Health status
* Traffic load

Advantages:

✅ Low latency
✅ Disaster recovery
✅ Better user experience

Disadvantages:

❌ More complex setup
❌ Higher cost

---

# Complete HLD flow

```text id="pb1kcz"
Users
   ↓
DNS / GSLB
   ↓
Regional LB
   ↓
Application LB
   ↓
App Servers
```

---

# Comparison Table

| Feature              | Network LB (L4) | Application LB (L7) |      DNS LB |           GSLB |
| -------------------- | --------------: | ------------------: | ----------: | -------------: |
| Works on             |         TCP/UDP |          HTTP/HTTPS |         DNS | Global traffic |
| Decision based on    |       IP + Port |        URL, Headers | Domain → IP | Region/Latency |
| Speed                |            Fast |              Medium |        Fast |         Medium |
| Intelligent routing  |              No |                 Yes |     Limited |            Yes |
| Multi-region support |              No |                  No |     Limited |            Yes |

Interview one-line summary:

**L4 LB routes using IP/ports, L7 LB routes using application data, DNS LB distributes via DNS records, and GSLB routes users to the best global region.**




In HLD, load balancers can also be classified as **Hardware Load Balancers** and **Software Load Balancers**.

# 1. Hardware Load Balancer

A hardware load balancer is a **physical networking device** dedicated to distributing traffic.

Structure:

```text
Users
   ↓
+------------------+
| Hardware LB      |
+------------------+
   ↓     ↓      ↓
Server1 Server2 Server3
```

Examples:

* F5 Networks BIG-IP
* Cisco load balancing appliances

Features:

* Dedicated hardware
* Very high performance
* Specialized processing chips

### Advantages

✅ Very fast processing
✅ High throughput
✅ Reliable for large enterprise systems

### Disadvantages

❌ Expensive
❌ Harder to scale
❌ Physical maintenance needed

### Use cases

* Large data centers
* Banking systems
* Enterprise infrastructure

---

# 2. Software Load Balancer

A software load balancer is an **application running on a server or cloud instance**.

Structure:

```text
Users
   ↓
+------------------+
| Software LB      |
+------------------+
   ↓      ↓      ↓
Server1 Server2 Server3
```

Examples:

* NGINX
* HAProxy
* Envoy Proxy

Features:

* Runs on normal servers
* Configuration-based
* Easy cloud deployment

### Advantages

✅ Lower cost
✅ Easy scaling
✅ Flexible configuration
✅ Works well with microservices

### Disadvantages

❌ Uses server resources
❌ Usually lower raw performance than dedicated hardware

---

# HLD example

Traditional enterprise:

```text
Users
   ↓
Hardware LB
   ↓
Application Servers
```

Cloud-native system:

```text
Users
   ↓
Software LB
   ↓
Microservices
```

---

# Comparison Table

| Feature        |          Hardware LB |          Software LB |
| -------------- | -------------------: | -------------------: |
| Type           |      Physical device | Application/software |
| Cost           |                 High |                  Low |
| Performance    |            Very high |          Medium–high |
| Scaling        |            Difficult |                 Easy |
| Flexibility    |                 Less |                 High |
| Maintenance    | Hardware maintenance |       Easier updates |
| Cloud friendly |              Limited |            Excellent |

---

For HLD interviews, remember:

**Hardware LB = dedicated appliance with high performance**
**Software LB = flexible, cheaper, cloud-friendly solution**

Today, many cloud and microservice systems mainly use software load balancers because they scale more easily.
