# Redis for a 6+ Year Laravel Developer

At your experience level, interviewers are usually **not testing Redis commands**. They want to know:

* Why Redis is used
* When to use Redis
* How Laravel integrates with Redis
* Performance benefits
* Caching strategies
* Queues and Sessions
* Real-world use cases

---

# What is Redis?

Redis (**Remote Dictionary Server**) is an **in-memory data store**.

Unlike MySQL:

| MySQL             | Redis                   |
| ----------------- | ----------------------- |
| Disk Based        | Memory Based            |
| Slower            | Very Fast               |
| Relational        | Key-Value               |
| Permanent Storage | Primarily Cache Storage |

Since Redis stores data in RAM, read/write operations are extremely fast.

---

# Why Do We Need Redis?

Suppose you have:

```sql
SELECT * FROM products WHERE category_id = 5;
```

Every request:

```text
User
 ↓
Laravel
 ↓
MySQL
 ↓
Response
```

Thousands of users hitting the same query:

* High DB load
* Slower response

Using Redis:

```text
User
 ↓
Laravel
 ↓
Redis
 ↓
Response
```

Response time drops significantly.

---

# Redis in Laravel

Install:

```bash
composer require predis/predis
```

or use PHP Redis extension.

Configure:

```env
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

---

# Most Common Redis Use Cases in Laravel

## 1. Caching

Most common use case.

Example:

```php
$users = Cache::remember(
    'users',
    3600,
    function () {
        return User::all();
    }
);
```

### What happens?

First Request:

```text
Redis
 ↓ Miss
MySQL
 ↓
Store in Redis
 ↓
Return Data
```

Second Request:

```text
Redis
 ↓ Hit
Return Data
```

No database query.

---

## Cache Hit vs Cache Miss

### Cache Hit

Data exists in Redis.

```text
Request
 ↓
Redis Found Data
 ↓
Response
```

Fast.

---

### Cache Miss

Data not found.

```text
Request
 ↓
Redis Miss
 ↓
MySQL Query
 ↓
Store in Redis
 ↓
Response
```

---

# TTL (Time To Live)

Cache should expire.

Example:

```php
Cache::put(
    'products',
    $products,
    now()->addHours(1)
);
```

After 1 hour:

```text
Redis automatically removes it.
```

---

# Cache Invalidation

One of the most important interview topics.

Suppose:

```text
Product Price = 100
```

Cached in Redis.

Admin updates:

```text
Product Price = 150
```

Redis still contains:

```text
100
```

Wrong data.

Solution:

```php
Cache::forget('product_1');
```

or

```php
Cache::tags(['products'])->flush();
```

---

# Redis as Session Driver

Default:

```env
SESSION_DRIVER=file
```

Better:

```env
SESSION_DRIVER=redis
```

Why?

Because when multiple servers are involved:

```text
Server A
Server B
Server C
```

Sessions remain centralized.

---

# Redis as Queue Driver

Very important Laravel interview topic.

```env
QUEUE_CONNECTION=redis
```

---

Without Queue

User Registration:

```text
Create User
 ↓
Send Welcome Email
 ↓
Generate PDF
 ↓
Send SMS
 ↓
Response
```

Slow.

---

With Redis Queue

```text
Create User
 ↓
Push Job To Redis
 ↓
Return Response
```

Background worker handles:

```text
Send Email
Generate PDF
Send SMS
```

Fast.

---

# Laravel Queue Example

Create Job:

```bash
php artisan make:job SendWelcomeEmail
```

Dispatch:

```php
SendWelcomeEmail::dispatch($user);
```

Worker:

```bash
php artisan queue:work
```

Redis stores jobs until worker processes them.

---

# Redis for Rate Limiting

Example:

```text
Login API
OTP API
Forgot Password API
```

Prevent abuse.

Laravel:

```php
RateLimiter::for('login', function () {
    return Limit::perMinute(5);
});
```

Redis tracks attempts.

---

# Redis Data Structures

Interviewers may ask this.

---

## String

Most common.

```redis
SET user_name Shubham
GET user_name
```

---

## Hash

Stores objects.

```redis
HSET user:1 name Shubham
HSET user:1 email abc@gmail.com
```

Equivalent:

```php
[
 'name' => 'Shubham',
 'email' => 'abc@gmail.com'
]
```

---

## List

FIFO queue.

```redis
LPUSH jobs job1
LPUSH jobs job2
```

Used in queue systems.

---

## Set

Unique values.

```redis
SADD roles admin
SADD roles admin
```

Only one stored.

---

## Sorted Set

Ranking system.

```redis
ZADD leaderboard 100 user1
```

Useful for:

* Leaderboards
* Top users
* Rankings

---

# Redis Persistence

Common senior-level question.

Redis is memory-based.

What if server restarts?

Data lost?

Redis provides:

### RDB

Snapshot backups.

```text
Save data every few minutes
```

---

### AOF

Append Only File.

Logs every write operation.

More durable.

---

# Redis Pub/Sub

Publisher sends message.

Subscribers receive message.

Example:

```text
Chat Application
```

```text
User A
 ↓
Publish Message
 ↓
Redis Channel
 ↓
User B Receives
```

Laravel Broadcasting concepts often use this pattern.

---

# Redis Clustering

When one Redis server is not enough.

Instead of:

```text
1 Redis Server
```

Use:

```text
Redis Node 1
Redis Node 2
Redis Node 3
```

Benefits:

* Scalability
* High Availability
* Fault Tolerance

---

# Real Interview Scenario

**Question: How would you improve performance of a product listing page?**

Answer:

1. Add database indexes.
2. Cache product listing in Redis.
3. Use pagination.
4. Cache frequently accessed categories.
5. Use queue for heavy background tasks.

---

# Frequently Asked Redis Interview Questions

## Basic

### What is Redis?

In-memory key-value data store used for caching, sessions, queues, rate limiting, and fast data access.

---

### Why Redis is faster than MySQL?

Redis stores data in RAM while MySQL reads from disk.

---

### What are Redis use cases?

* Cache
* Sessions
* Queues
* Rate Limiting
* Pub/Sub
* Real-time applications

---

### Difference between Redis and Memcached?

Redis:

* Multiple data structures
* Persistence
* Pub/Sub
* Replication

Memcached:

* Simple key-value cache only

---

## Intermediate

### What is cache hit and cache miss?

Cache Hit:

* Data found in Redis.

Cache Miss:

* Data not found, fetched from DB.

---

### What is TTL?

Time To Live.

Expiration time after which Redis removes data automatically.

---

### How do you clear Redis cache?

Laravel:

```bash
php artisan cache:clear
```

Redis:

```bash
redis-cli flushall
```

---

### Why use Redis for sessions?

Shared session storage across multiple servers.

---

### Why use Redis for queues?

Fast background job processing.

---

## Advanced

### What is Redis persistence?

Mechanism to save memory data to disk.

Types:

* RDB
* AOF

---

### What is Redis clustering?

Multiple Redis nodes working together for scalability and high availability.

---

### What happens if Redis goes down?

Possible answers:

* Application falls back to DB.
* Cache misses increase.
* Queue processing may stop.
* Sessions may be lost if Redis is session store.

---

### Explain Redis in your current project.

A strong answer:

> We use Redis primarily for caching frequently accessed data and as the queue driver for Laravel jobs. Product/category data is cached to reduce MySQL load, and email/notification jobs are processed asynchronously using Redis queues. This improves response times and overall application performance.

---

# Must-Memorize Senior-Level Questions

1. Why Redis instead of MySQL cache table?
2. Cache Hit vs Cache Miss?
3. What is TTL?
4. How do you invalidate cache?
5. Why use Redis for sessions?
6. Why use Redis for queues?
7. Difference between Redis and Memcached?
8. What happens if Redis server goes down?
9. Explain Redis persistence.
10. Explain how Redis is used in your Laravel project.

If you can answer these 10 confidently with practical examples from Laravel, you'll handle the vast majority of Redis questions asked in Senior Laravel interviews.
