# CI/CD for a 6+ Year Laravel Developer

For a Senior Laravel interview, you are usually expected to understand:

* What CI/CD is
* Why it's important
* How code moves from developer machine to production
* GitHub Actions / GitLab CI / Jenkins basics
* Deployment strategies
* Rollbacks
* Zero-downtime deployments

You are **not expected to be a DevOps engineer**, but you should be comfortable discussing deployment pipelines.

---

# What is CI/CD?

CI/CD stands for:

```text
CI = Continuous Integration
CD = Continuous Delivery / Continuous Deployment
```

The goal is:

```text
Write Code
     ↓
Test Code
     ↓
Build Code
     ↓
Deploy Code
```

automatically.

---

# Problem Before CI/CD

Traditional deployment:

```text
Developer
   ↓
SSH into Server
   ↓
git pull
   ↓
composer install
   ↓
php artisan migrate
   ↓
restart queue
```

Problems:

* Human errors
* Missed steps
* Inconsistent deployments
* Difficult rollbacks

---

# CI/CD Solution

Developer pushes code:

```bash
git push origin main
```

Pipeline automatically:

```text
Run Tests
 ↓
Build
 ↓
Deploy
 ↓
Verify
```

No manual intervention.

---

# Understanding CI

## Continuous Integration

Whenever code is pushed:

```text
Developer
   ↓
Git Repository
   ↓
Automatic Validation
```

---

Typical CI Steps

```text
Pull Code
 ↓
Install Dependencies
 ↓
Run Tests
 ↓
Code Quality Checks
 ↓
Build
```

---

Example:

```text
Developer Pushes Code
        ↓
GitHub
        ↓
GitHub Actions
        ↓
Run PHPUnit Tests
```

If tests fail:

```text
Deployment Blocked
```

---

# Understanding CD

## Continuous Delivery

After CI succeeds:

```text
Build Ready
```

Human approval required.

```text
Approve
 ↓
Deploy
```

---

## Continuous Deployment

After CI succeeds:

```text
Build Ready
 ↓
Deploy Automatically
```

No approval.

---

# Continuous Delivery vs Continuous Deployment

| Delivery              | Deployment         |
| --------------------- | ------------------ |
| Manual Approval       | Fully Automatic    |
| Safer                 | Faster             |
| Common in Enterprises | Common in Startups |

---

# CI/CD Workflow

Typical Laravel workflow:

```text
Developer
   ↓
Git Push
   ↓
GitHub
   ↓
Pipeline
   ↓
Run Tests
   ↓
Build
   ↓
Deploy
   ↓
Production
```

---

# Popular CI/CD Tools

## GitHub Actions

Most common nowadays.

Official:

[GitHub Actions](https://github.com/features/actions?utm_source=chatgpt.com)

---

## GitLab CI/CD

Official:

[GitLab CI/CD](https://about.gitlab.com/stages-devops-lifecycle/continuous-integration/?utm_source=chatgpt.com)

---

## Jenkins

Official:

[Jenkins](https://www.jenkins.io/?utm_source=chatgpt.com)

---

## Bitbucket Pipelines

Official:

[Bitbucket Pipelines](https://www.atlassian.com/software/bitbucket/features/pipelines?utm_source=chatgpt.com)

---

# CI/CD for Laravel

Typical deployment steps:

```text
git pull
composer install
php artisan migrate
php artisan config:cache
php artisan route:cache
php artisan queue:restart
```

Pipeline automates these.

---

# GitHub Actions Example

Workflow file:

```yaml
name: Laravel CI

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
```

---

# Typical Laravel Pipeline

```text
Checkout Code
 ↓
Install PHP
 ↓
Install Composer Dependencies
 ↓
Run PHPUnit Tests
 ↓
Build
 ↓
Deploy
```

---

# Build Stage

What happens during build?

```text
Install Dependencies
Generate Artifacts
Package Application
```

Example:

```bash
composer install --no-dev
```

---

# Testing Stage

Most companies expect:

```text
Unit Tests
Feature Tests
```

Run before deployment.

Example:

```bash
php artisan test
```

or

```bash
vendor/bin/phpunit
```

---

# Deployment Stage

Pipeline connects to server.

Example:

```text
GitHub Actions
      ↓
EC2 Server
```

Executes:

```bash
git pull
composer install
php artisan migrate
```

---

# Environment Variables

Never store secrets in code.

Bad:

```env
DB_PASSWORD=123456
```

inside repository.

---

Good:

```text
GitHub Secrets
AWS Secrets
GitLab Variables
```

---

Example:

```text
DB_PASSWORD
AWS_ACCESS_KEY
JWT_SECRET
```

stored securely.

---

# Laravel Optimization Commands

Frequently used during deployment.

---

## Config Cache

```bash
php artisan config:cache
```

Combines configuration files.

Improves performance.

---

## Route Cache

```bash
php artisan route:cache
```

Caches routes.

---

## View Cache

```bash
php artisan view:cache
```

Caches Blade templates.

---

## Event Cache

```bash
php artisan event:cache
```

Laravel 11/12 optimization.

---

# Queue Restart

Common deployment step.

After deployment:

```bash
php artisan queue:restart
```

Workers reload latest code.

---

# Database Migrations

Pipeline often executes:

```bash
php artisan migrate --force
```

Why force?

Because production environment disables migrations by default.

---

# Rollback Strategy

Important senior-level topic.

Deployment fails.

Need rollback.

Laravel:

```bash
php artisan migrate:rollback
```

Git:

```bash
git checkout previous_commit
```

---

Good pipelines support:

```text
One-Click Rollback
```

---

# Blue-Green Deployment

Popular interview topic.

Current:

```text
Blue Environment
```

Deploy new version:

```text
Green Environment
```

Switch traffic:

```text
Users
 ↓
Green
```

Benefits:

* Zero downtime
* Easy rollback

---

Visualization:

```text
Blue (Current)
Green (New)
```

---

# Rolling Deployment

Common with Kubernetes.

Current:

```text
Server1
Server2
Server3
```

Update:

```text
Server1 Updated
Server2 Updated
Server3 Updated
```

Gradually.

No downtime.

---

# Zero-Downtime Deployment

Goal:

```text
Users Never Notice Deployment
```

Methods:

* Load Balancer
* Rolling Updates
* Blue-Green Deployments

---

# Monitoring After Deployment

Deployment success isn't enough.

Need monitoring.

Tools:

* AWS CloudWatch
* Datadog
* New Relic
* Grafana

Monitor:

```text
CPU
Memory
Errors
Response Time
```

---

# CI/CD with Docker

Very common modern setup.

Pipeline:

```text
Push Code
 ↓
Build Docker Image
 ↓
Push Image
 ↓
Deploy Container
```

---

Example:

```text
GitHub Actions
     ↓
Docker Build
     ↓
AWS ECR
     ↓
ECS / Kubernetes
```

---

# CI/CD with Kubernetes

Pipeline:

```text
Push Code
 ↓
Build Docker Image
 ↓
Push to Registry
 ↓
Update Kubernetes Deployment
```

---

Kubernetes performs:

```text
Rolling Update
```

automatically.

---

# Real Laravel Production Flow

```text
Developer
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Run Tests
   ↓
Build Docker Image
   ↓
Deploy EC2/Kubernetes
   ↓
Restart Queue Workers
   ↓
Production
```

---

# Frequently Asked CI/CD Interview Questions

## Basic

### What is CI?

Continuous Integration.

Automatically validates code after every commit.

---

### What is CD?

Continuous Delivery/Deployment.

Automates release process.

---

### Difference between CI and CD?

CI:

```text
Build + Test
```

CD:

```text
Deploy
```

---

### Why CI/CD?

* Faster releases
* Fewer errors
* Better consistency

---

## Intermediate

### What tools have you used?

Possible answers:

* GitHub Actions
* GitLab CI
* Jenkins
* Bitbucket Pipelines

---

### Why run tests in CI?

Prevents broken code reaching production.

---

### Why use environment variables?

Protect secrets and support different environments.

---

### Why restart queues after deployment?

Workers keep old code in memory.

Need reload.

---

### What optimization commands do you run?

```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

---

## Advanced

### What is Zero-Downtime Deployment?

Deployment without interrupting users.

---

### What is Blue-Green Deployment?

Two environments.

Switch traffic after validation.

---

### What is Rolling Deployment?

Update servers gradually.

---

### What is Rollback?

Reverting to a previous stable release.

---

### How would you deploy a Laravel application?

A good answer:

```text
1. Push code to GitHub
2. Trigger CI pipeline
3. Run tests
4. Build application/Docker image
5. Deploy to server
6. Run migrations
7. Cache config/routes
8. Restart queues
9. Monitor deployment
```

---

# Must-Memorize Senior-Level CI/CD Questions

1. What is CI?
2. What is CD?
3. Difference between Continuous Delivery and Continuous Deployment?
4. Why run tests in CI?
5. What is GitHub Actions?
6. Why use environment variables?
7. Why restart queue workers after deployment?
8. What is Zero-Downtime Deployment?
9. What is Blue-Green Deployment?
10. Explain your deployment process.

---

# Interview Answer You Can Use

**"Describe your CI/CD process."**

> In a typical Laravel project, developers push code to GitHub. A CI pipeline runs automated tests and validates the build. If successful, the deployment pipeline connects to the target environment, installs dependencies, runs database migrations, clears and rebuilds Laravel caches, restarts queue workers, and performs health checks. For containerized applications, Docker images are built and deployed through Kubernetes or cloud infrastructure. This ensures consistent, automated, and reliable deployments with minimal manual intervention.

This answer is strong enough for most Senior Laravel roles in the ₹15–20+ LPA range.

The final major topic after this is **System Design**, which is often the deciding factor between a ₹10 LPA developer and a ₹15–20+ LPA Senior Backend Developer.
