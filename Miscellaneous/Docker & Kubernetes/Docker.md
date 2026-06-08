# Docker for a 6+ Year Laravel Developer

For a Senior Laravel/PHP interview, you are **not expected to be a DevOps engineer**. You should understand:

* Why Docker is used
* How containers work
* Dockerfile
* Docker Compose
* How to run Laravel in Docker
* Basic networking, volumes, and images
* Common production concepts

---

# What is Docker?

Docker is a containerization platform that packages:

* Application code
* PHP version
* Composer
* Extensions
* Dependencies

into a portable container.

---

## Problem Before Docker

Developer A:

```text
PHP 8.3
MySQL 8
Redis 7
```

Developer B:

```text
PHP 8.1
MySQL 5.7
Redis 6
```

Application works on one machine but fails on another.

Common statement:

```text
"It works on my machine."
```

---

## Docker Solution

Docker packages everything.

```text
Laravel App
PHP
Composer
Extensions
Configuration
```

Anyone can run the same container.

Result:

```text
Works everywhere
```

---

# What is a Container?

A container is a running instance of an image.

Think:

```text
Image = Blueprint
Container = Running Application
```

Example:

```text
Image
 ↓
Start
 ↓
Container
```

---

# What is a Docker Image?

An image is a read-only template.

Example:

```bash
php:8.3-fpm
mysql:8
redis:7
nginx:latest
```

These images are downloaded from Docker Hub.

---

# What is Docker Hub?

Docker Hub is similar to GitHub but for Docker images.

Official images:

* PHP
* MySQL
* Redis
* Nginx
* Ubuntu

Available at:

[Docker Hub](https://hub.docker.com?utm_source=chatgpt.com)

---

# Docker Architecture

```text
Docker Client
      ↓
Docker Engine
      ↓
Containers
```

Commands:

```bash
docker run
docker build
docker stop
```

communicate with Docker Engine.

---

# Installing Docker

Windows:

[Docker Desktop](https://www.docker.com/products/docker-desktop?utm_source=chatgpt.com)

Most Laravel teams use Docker Desktop locally.

---

# Dockerfile

A Dockerfile defines how an image should be built.

Example:

```dockerfile
FROM php:8.3-fpm

WORKDIR /var/www

COPY . .

RUN docker-php-ext-install pdo pdo_mysql

RUN apt-get update

CMD ["php-fpm"]
```

---

## Dockerfile Explained

### Base Image

```dockerfile
FROM php:8.3-fpm
```

Start with PHP image.

---

### Working Directory

```dockerfile
WORKDIR /var/www
```

Equivalent:

```bash
cd /var/www
```

---

### Copy Application

```dockerfile
COPY . .
```

Copies Laravel project.

---

### Install Extensions

```dockerfile
RUN docker-php-ext-install pdo_mysql
```

Needed for MySQL.

---

### Start Container

```dockerfile
CMD ["php-fpm"]
```

Starts PHP-FPM.

---

# Building an Image

```bash
docker build -t laravel-app .
```

Explanation:

```text
-t laravel-app
```

Image name.

---

# Running a Container

```bash
docker run laravel-app
```

Container starts from image.

---

# Important Commands

## List Running Containers

```bash
docker ps
```

---

## List All Containers

```bash
docker ps -a
```

---

## Stop Container

```bash
docker stop container_id
```

---

## Remove Container

```bash
docker rm container_id
```

---

## Remove Image

```bash
docker rmi image_name
```

---

# Why Docker Alone Is Not Enough

Laravel application usually needs:

```text
Laravel
MySQL
Redis
Nginx
```

Managing all separately becomes difficult.

Solution:

```text
Docker Compose
```

---

# Docker Compose

Docker Compose manages multiple containers.

Example:

```yaml
version: '3'

services:

  app:
    build: .

  mysql:
    image: mysql:8

  redis:
    image: redis:7

  nginx:
    image: nginx
```

---

# Starting Compose

```bash
docker compose up -d
```

Creates:

```text
Laravel
MySQL
Redis
Nginx
```

together.

---

# Stopping Compose

```bash
docker compose down
```

---

# Laravel Docker Architecture

Typical setup:

```text
Browser
   ↓
Nginx
   ↓
Laravel Container
   ↓
MySQL Container
   ↓
Redis Container
```

---

# Docker Volumes

Problem:

Container deleted.

Data lost.

Example:

```text
MySQL Container Removed
 ↓
Database Gone
```

---

Solution:

Volumes.

```yaml
volumes:
  mysql-data:
```

Store data outside container.

---

# Docker Networking

Containers communicate using service names.

Example:

```yaml
services:

 mysql:
```

Laravel .env:

```env
DB_HOST=mysql
```

Not:

```env
DB_HOST=127.0.0.1
```

Because containers use Docker network.

---

# Docker Port Mapping

Example:

```yaml
ports:
  - "8000:80"
```

Meaning:

```text
Host Port 8000
 ↓
Container Port 80
```

Access:

```text
localhost:8000
```

---

# Environment Variables

Example:

```env
APP_ENV=local
DB_HOST=mysql
REDIS_HOST=redis
```

Passed into containers.

---

# Docker vs Virtual Machine

Most asked question.

| Docker             | VM                      |
| ------------------ | ----------------------- |
| Lightweight        | Heavy                   |
| Shares Host Kernel | Own OS                  |
| Fast Startup       | Slow Startup            |
| MBs                | GBs                     |
| Container Based    | Hardware Virtualization |

---

### Visualization

VM:

```text
Hardware
 ↓
Host OS
 ↓
Hypervisor
 ↓
Guest OS
 ↓
Application
```

Docker:

```text
Hardware
 ↓
Host OS
 ↓
Docker Engine
 ↓
Container
```

Less overhead.

---

# Docker Layers

Each Dockerfile instruction creates a layer.

Example:

```dockerfile
FROM php:8.3

COPY .

RUN composer install
```

Layers:

```text
Layer 1
Layer 2
Layer 3
```

Benefits:

* Faster builds
* Cached layers

---

# Multi-Stage Build

Production optimization.

Example:

```dockerfile
FROM composer AS builder

RUN composer install

FROM php:8.3-fpm

COPY --from=builder /app /app
```

Benefits:

* Smaller image
* Better security

---

# Docker Logs

Check logs:

```bash
docker logs container_id
```

Very common in troubleshooting.

---

# Enter Running Container

```bash
docker exec -it container_id bash
```

Useful for:

```bash
php artisan migrate
php artisan tinker
```

inside container.

---

# Laravel Sail

Official Laravel Docker environment.

Documentation:

[Laravel Sail Documentation](https://laravel.com/docs/sail?utm_source=chatgpt.com)

Provides:

```text
Laravel
MySQL
Redis
Mailpit
Meilisearch
```

out of the box.

---

# Real Interview Scenario

### Question

How would you set up Laravel using Docker?

Answer:

```text
1. Create Dockerfile for Laravel.
2. Use Nginx container.
3. Use MySQL container.
4. Use Redis container.
5. Configure docker-compose.yml.
6. Map ports.
7. Use volumes for persistence.
8. Run docker compose up -d.
```

---

# Common Production Setup

```text
Load Balancer
       ↓
Nginx
       ↓
Laravel Container
       ↓
Redis
       ↓
MySQL
```

---

# Frequently Asked Docker Interview Questions

## Basic

### What is Docker?

Containerization platform that packages application and dependencies into portable containers.

---

### What problem does Docker solve?

Environment inconsistency.

```text
Works on my machine
```

problem.

---

### What is a Docker Image?

Blueprint/template used to create containers.

---

### What is a Container?

Running instance of an image.

---

### What is Docker Hub?

Repository for Docker images.

---

## Intermediate

### What is Docker Compose?

Tool for managing multiple containers.

---

### Difference between Dockerfile and Docker Compose?

Dockerfile:

```text
Builds Image
```

Compose:

```text
Runs Multiple Containers
```

---

### What are Volumes?

Persistent storage outside container lifecycle.

---

### Why use Volumes?

Prevents data loss.

Especially:

```text
MySQL Data
Uploads
Logs
```

---

### What is Port Mapping?

Connect host port to container port.

Example:

```yaml
8000:80
```

---

### What is Docker Networking?

Allows containers to communicate via service names.

---

## Advanced

### Docker vs Virtual Machine?

Docker shares host OS kernel.

VM runs separate OS.

Docker is lighter and faster.

---

### What are Docker Layers?

Each instruction in Dockerfile creates a layer.

Improves build caching.

---

### What is Multi-Stage Build?

Build application in one stage and copy artifacts into final image.

Produces smaller production images.

---

### How do you debug a running container?

```bash
docker logs
docker exec -it
```

---

### What happens if a container crashes?

Container stops.

In production, tools like Kubernetes or Docker restart policies can automatically restart it.

---

# Must-Memorize Senior-Level Docker Questions

1. What problem does Docker solve?
2. Difference between Image and Container?
3. What is Dockerfile?
4. What is Docker Compose?
5. Why use Volumes?
6. What is Port Mapping?
7. Docker vs Virtual Machine?
8. How do containers communicate?
9. How do you access a running container?
10. Explain your Laravel Docker setup.

### Sample Answer for "How do you use Docker in your project?"

> We use Docker Compose to run Laravel, Nginx, MySQL, and Redis as separate containers. The Laravel application is built using a Dockerfile, services communicate through Docker networking, and MySQL data is stored using volumes for persistence. This ensures consistent development environments across all developers and simplifies deployment.

Once you're comfortable with Docker, the next logical topic is **Kubernetes**, because interviewers often ask: **"You know Docker—how would you manage 100 containers in production?"** That's where Kubernetes comes in.
