# System Design for a 6+ Year Laravel Developer

This is the topic that most often separates:

```text
Laravel Developer (₹8–12 LPA)
```

from

```text
Senior Backend Developer (₹15–25+ LPA)
```

Interviewers are not looking for perfect architecture. They want to see whether you can think about:

* Scalability
* Performance
* Reliability
* Availability
* Caching
* Database design
* Queues
* Trade-offs

---

# What is System Design?

System Design is the process of designing software that can handle:

```text
More Users
More Traffic
More Data
More Requests
```

while remaining:

```text
Fast
Reliable
Maintainable
```

---

# Example

Small Application

```text
Users
 ↓
Laravel
 ↓
MySQL
```

Works fine.

---

As traffic grows:

```text
100,000 Users
```

Problems appear:

* Slow responses
* Database overload
* Server crashes

System design solves these issues.

---

# Fundamental Components

A typical production system:

```text
Client
 ↓
Load Balancer
 ↓
Application Servers
 ↓
Redis
 ↓
Database
```

---

# 1. Load Balancer

Most asked concept.

---

## Problem

Single server:

```text
Users
 ↓
Server
```

If server crashes:

```text
Application Down
```

---

## Solution

```text
Users
 ↓
Load Balancer
 ↓
Server 1
Server 2
Server 3
```

---

Benefits:

* High availability
* Better performance
* Fault tolerance

---

## Interview Question

### What happens if Server 2 crashes?

Load balancer stops sending traffic to it.

Remaining servers continue serving requests.

---

# 2. Caching

One of the most important concepts.

---

## Problem

Every request hits database.

```text
User
 ↓
Laravel
 ↓
MySQL
```

Expensive.

---

## Solution

Redis.

```text
User
 ↓
Laravel
 ↓
Redis
```

---

# Cache Aside Pattern

Most common caching strategy.

Flow:

```text
Request
 ↓
Redis
 ↓ Miss
MySQL
 ↓
Store in Redis
 ↓
Response
```

---

Second request:

```text
Redis
 ↓ Hit
Response
```

---

## Interview Question

### What is Cache Hit?

Data found in cache.

---

### What is Cache Miss?

Data not found in cache.

---

# 3. Database Indexing

Extremely common question.

---

## Without Index

```sql
SELECT * FROM users
WHERE email='abc@gmail.com';
```

MySQL scans:

```text
Row 1
Row 2
Row 3
...
Row 1,000,000
```

Slow.

---

## With Index

```sql
CREATE INDEX idx_email
ON users(email);
```

MySQL quickly locates data.

---

## When to Add Index?

Frequently searched columns:

```text
email
mobile
user_id
status
created_at
```

---

## Interview Question

### Why not index every column?

Indexes:

* Consume storage
* Slow writes
* Increase maintenance

---

# 4. Database Scaling

---

## Vertical Scaling

Increase server resources.

```text
4 GB RAM
 ↓
16 GB RAM
```

Easy but limited.

---

## Horizontal Scaling

Add more servers.

```text
DB1
DB2
DB3
```

Preferred at large scale.

---

# Read Replica

Very common interview topic.

---

## Problem

Heavy read traffic.

```text
SELECT
SELECT
SELECT
SELECT
```

Main DB overloaded.

---

## Solution

```text
Master DB
      ↓
Replica DB
      ↓
Replica DB
```

---

Writes:

```text
INSERT
UPDATE
DELETE
```

go to Master.

Reads:

```text
SELECT
```

go to Replicas.

---

# 5. Queue Systems

Senior interview favorite.

---

## Problem

User Registration:

```text
Create User
Send Email
Generate PDF
Send SMS
```

Response becomes slow.

---

## Solution

Queues.

```text
Create User
 ↓
Push Job
 ↓
Response
```

Background worker handles:

```text
Email
PDF
SMS
```

---

Laravel Example:

```php
SendEmailJob::dispatch();
```

---

Interview Question:

### Why Queue?

To move heavy tasks out of request lifecycle.

---

# 6. Rate Limiting

Protect APIs.

---

Example:

```text
OTP API
```

Without limit:

```text
1,000 Requests/Second
```

Possible abuse.

---

Solution:

```text
5 Requests/Minute
```

using Redis.

---

Laravel:

```php
RateLimiter::for(...)
```

---

# 7. Session Management

Single server:

```text
Session File
```

Works.

---

Multiple servers:

```text
Server 1
Server 2
Server 3
```

Problem:

User logs in on Server 1.

Next request hits Server 2.

Session missing.

---

Solution:

```text
Redis
```

Centralized session storage.

---

# 8. CDN

Content Delivery Network.

---

Without CDN:

```text
India User
 ↓
US Server
```

Slow.

---

With CDN:

```text
India User
 ↓
Nearby CDN Edge
```

Fast.

---

Examples:

* CloudFront
* Cloudflare

---

# 9. High Availability

Most asked architecture concept.

---

Goal:

```text
No Single Point of Failure
```

Bad:

```text
Users
 ↓
One Server
```

---

Better:

```text
Users
 ↓
Load Balancer
 ↓
Server 1
Server 2
Server 3
```

---

# 10. CAP Theorem (Interview Favorite)

Distributed systems can guarantee only two:

```text
Consistency
Availability
Partition Tolerance
```

Not all three simultaneously.

---

## Consistency

All nodes return same data.

---

## Availability

System always responds.

---

## Partition Tolerance

System survives network failures.

---

Most modern systems choose:

```text
Availability
+
Partition Tolerance
```

---

# 11. Horizontal vs Vertical Scaling

Interview classic.

### Vertical

```text
Server
 ↓
More RAM
More CPU
```

---

### Horizontal

```text
Server 1
Server 2
Server 3
```

---

### Which is Better?

Usually:

```text
Horizontal Scaling
```

because it scales further and improves availability.

---

# 12. URL Shortener Design

Very popular interview problem.

---

## Requirements

Input:

```text
https://example.com/products/123
```

Output:

```text
abc123
```

---

## Architecture

```text
User
 ↓
Laravel API
 ↓
MySQL
 ↓
Redis Cache
```

---

## Flow

Create:

```text
Long URL
 ↓
Generate Hash
 ↓
Store
 ↓
Return Short URL
```

---

Access:

```text
Short URL
 ↓
Redis
 ↓ Hit
Redirect
```

---

# 13. Notification System

Popular senior-level question.

---

Architecture:

```text
User Action
      ↓
Queue
      ↓
Workers
      ↓
Email
SMS
Push
```

---

Benefits:

* Fast response
* Independent processing

---

# 14. E-Commerce System Design

Very common.

---

Architecture:

```text
Users
 ↓
Load Balancer
 ↓
Laravel Servers
 ↓
Redis
 ↓
MySQL
```

---

Order Flow:

```text
Place Order
 ↓
Queue
 ↓
Payment
Inventory
Notification
Invoice
```

---

# 15. Microservices (Know Basics)

You don't need deep expertise.

---

Monolith:

```text
Laravel App
```

Everything together.

---

Microservices:

```text
User Service
Order Service
Payment Service
Notification Service
```

Separate systems.

---

Benefits:

* Independent deployment
* Independent scaling

---

Challenges:

* Complexity
* Monitoring
* Communication

---

# Common System Design Interview Questions

---

## Design a URL Shortener

Focus:

* Database schema
* Hash generation
* Redis caching

---

## Design a Notification System

Focus:

* Queue
* Workers
* Retry mechanism

---

## Design an E-commerce Website

Focus:

* Products
* Orders
* Payments
* Inventory

---

## Design a File Upload System

Focus:

* S3
* CDN
* Metadata in DB

---

## Design a Rate Limiter

Focus:

* Redis
* TTL

---

# Important Interview Questions

### What is Load Balancer?

Distributes traffic among servers.

---

### What is Caching?

Storing frequently accessed data in memory.

---

### Why Redis?

Fast in-memory storage.

---

### What is Cache Invalidation?

Removing stale cache after data changes.

---

### What is Database Index?

Data structure that speeds up searches.

---

### Why Use Queues?

Move long-running tasks to background processing.

---

### What is Read Replica?

Database copy used for read operations.

---

### Difference Between Horizontal and Vertical Scaling?

Vertical:

```text
Bigger Server
```

Horizontal:

```text
More Servers
```

---

### What is High Availability?

System remains operational despite failures.

---

### How Would You Scale Laravel?

A strong answer:

```text
1. Add Redis caching
2. Add DB indexes
3. Use queues
4. Move sessions to Redis
5. Add Load Balancer
6. Add multiple Laravel servers
7. Use CDN
8. Use Read Replicas
```

---

# 10 Questions You Must Be Able To Answer

1. How would you scale a Laravel application?
2. What is a Load Balancer?
3. What is Redis caching?
4. Cache Hit vs Cache Miss?
5. What is Database Indexing?
6. Why use Queues?
7. What is a Read Replica?
8. Horizontal vs Vertical Scaling?
9. How would you design a URL Shortener?
10. How would you design a Notification System?

---

# Senior Laravel Interview Answer

**"How would you improve the performance of a slow Laravel application?"**

A strong answer:

> First, I would identify bottlenecks using profiling and query analysis. I would optimize database queries and add appropriate indexes. Frequently accessed data would be cached in Redis. Heavy operations such as emails, notifications, PDF generation, and third-party API calls would be moved to queues. For increasing traffic, I would place the application behind a load balancer, run multiple Laravel instances, move sessions to Redis, store files in S3, and use a CDN for static assets. If database reads become a bottleneck, I would introduce read replicas. This approach improves performance, scalability, and availability.

If you can confidently discuss Redis, Docker, Kubernetes, Kafka, AWS, CI/CD, and the System Design concepts above, you'll be well prepared for many Senior Laravel Developer interviews in the ₹15–20+ LPA range.
