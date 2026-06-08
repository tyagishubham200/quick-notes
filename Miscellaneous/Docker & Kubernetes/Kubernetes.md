# Kubernetes for a 6+ Year Laravel Developer

For Senior Laravel interviews, Kubernetes is usually asked at a **conceptual level**, not at the level expected from a DevOps/SRE engineer.

Interviewers typically want to know:

* Why Kubernetes exists
* How it relates to Docker
* What Pods, Deployments, Services, and Ingress are
* How scaling works
* How high availability works

---

# Why Kubernetes?

Suppose your application runs in Docker.

```text
Laravel Container
```

Works great.

Now traffic increases.

You need:

```text
Laravel Container 1
Laravel Container 2
Laravel Container 3
Laravel Container 4
```

Questions arise:

* How do you deploy updates?
* How do you restart crashed containers?
* How do you scale automatically?
* How do users reach the correct container?

Managing this manually becomes difficult.

Kubernetes solves this problem.

---

# What is Kubernetes?

Kubernetes (K8s) is a container orchestration platform.

It manages:

* Containers
* Scaling
* Networking
* Load Balancing
* Self-healing
* Deployments

Think of it as:

```text
Docker runs containers

Kubernetes manages containers
```

---

# Docker vs Kubernetes

| Docker             | Kubernetes          |
| ------------------ | ------------------- |
| Creates Containers | Manages Containers  |
| Single Host Focus  | Cluster Focus       |
| Manual Scaling     | Automatic Scaling   |
| Basic Restart      | Self-Healing        |
| Simple Deployment  | Rolling Deployments |

---

# Real World Example

Imagine an e-commerce site.

Without Kubernetes:

```text
Docker Container
      ↓
Crash
      ↓
Application Down
```

With Kubernetes:

```text
Docker Container
      ↓
Crash
      ↓
Kubernetes Detects Failure
      ↓
Creates New Container
```

Users may not even notice.

---

# Kubernetes Architecture

At a high level:

```text
Master Node
    ↓
Worker Nodes
    ↓
Pods
```

---

# Master Node

Controls the cluster.

Responsible for:

* Scheduling
* Monitoring
* Scaling
* Deployment decisions

Think:

```text
Brain of Kubernetes
```

---

# Worker Node

Machines where applications actually run.

Example:

```text
Node 1
Node 2
Node 3
```

Each node runs:

```text
Pods
```

---

# What is a Pod?

Most important Kubernetes interview question.

A Pod is the smallest deployable unit in Kubernetes.

Contains:

```text
Laravel Container
```

or

```text
Laravel Container
Redis Sidecar Container
```

---

Visualization:

```text
Pod
 └── Laravel Container
```

---

# Why Pods Instead of Containers?

Kubernetes manages Pods.

Not individual containers.

This provides:

* Networking
* Storage
* Lifecycle management

---

# What is a Deployment?

A Deployment manages Pods.

Example:

```text
Deployment
      ↓
3 Pods
```

---

Without Deployment:

```text
Pod Deleted
 ↓
Gone Forever
```

---

With Deployment:

```text
Pod Deleted
 ↓
Kubernetes Creates New Pod
```

---

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

spec:
  replicas: 3
```

Meaning:

```text
Always Keep 3 Pods Running
```

---

# What are Replicas?

Replicas = Number of Pod Copies.

Example:

```yaml
replicas: 3
```

Creates:

```text
Pod 1
Pod 2
Pod 3
```

Benefits:

* Load distribution
* High availability

---

# What is a Service?

Pods have dynamic IP addresses.

Problem:

```text
Pod Deleted
 ↓
New IP Assigned
```

Applications break.

Solution:

```text
Service
```

Provides a stable endpoint.

---

Example:

```text
Laravel Pods
     ↓
Service
     ↓
Single Stable Address
```

---

# Service Types

## ClusterIP

Internal communication.

Example:

```text
Laravel → Redis
Laravel → MySQL
```

---

## NodePort

Exposes service externally.

---

## LoadBalancer

Cloud-based external access.

AWS creates:

```text
AWS Load Balancer
```

automatically.

---

# What is Ingress?

Ingress manages external HTTP traffic.

Without Ingress:

```text
App1
App2
App3

Need separate Load Balancers
```

Expensive.

---

With Ingress:

```text
api.example.com
      ↓
Laravel Service

admin.example.com
      ↓
Admin Service
```

One entry point.

---

# Kubernetes Networking

Every Pod gets:

```text
Own IP Address
```

Pods communicate directly.

Example:

```text
Laravel Pod
     ↓
Redis Pod
```

through Kubernetes networking.

---

# ConfigMaps

Store configuration.

Instead of:

```env
APP_NAME=Laravel
```

inside image.

Store separately:

```text
ConfigMap
```

Benefits:

* Change config
* No rebuild required

---

# Secrets

Store sensitive information.

Example:

```text
DB_PASSWORD
AWS_KEY
JWT_SECRET
```

Never hardcode.

Use:

```text
Kubernetes Secret
```

---

# Persistent Volumes

Problem:

```text
Pod Deleted
 ↓
Data Lost
```

Solution:

```text
Persistent Volume
```

Data survives Pod recreation.

Used for:

* MySQL
* PostgreSQL
* Uploads

---

# Horizontal Scaling

Most asked interview topic.

Current:

```text
1 Pod
```

Traffic increases.

Scale:

```text
5 Pods
```

Command:

```bash
kubectl scale deployment laravel --replicas=5
```

---

Visualization:

```text
Users
   ↓
Service
   ↓
Pod 1
Pod 2
Pod 3
Pod 4
Pod 5
```

---

# Auto Scaling

Even better.

Kubernetes watches CPU.

Example:

```text
CPU > 80%
```

Automatically:

```text
3 Pods
 ↓
6 Pods
```

When traffic decreases:

```text
6 Pods
 ↓
3 Pods
```

---

# Rolling Updates

Production deployment feature.

Current:

```text
Laravel v1
```

Deploy:

```text
Laravel v2
```

Kubernetes updates gradually.

```text
Pod 1 → v2
Pod 2 → v2
Pod 3 → v2
```

No downtime.

---

# Rollback

Deployment fails.

Kubernetes can revert.

```text
v2 Failed
 ↓
Rollback
 ↓
v1
```

---

# Self-Healing

One Pod crashes.

```text
Pod Crash
```

Kubernetes:

```text
Detect Failure
 ↓
Create New Pod
```

Automatically.

---

# Kubernetes in AWS

Common setup:

```text
Internet
    ↓
AWS Load Balancer
    ↓
Ingress
    ↓
Laravel Pods
    ↓
Redis
    ↓
RDS MySQL
```

---

# Real Laravel Architecture

```text
Users
   ↓
Load Balancer
   ↓
Ingress
   ↓
Laravel Pods (3)
   ↓
Redis
   ↓
AWS RDS MySQL
```

This is a common production-grade architecture.

---

# kubectl Commands

## View Pods

```bash
kubectl get pods
```

---

## View Services

```bash
kubectl get svc
```

---

## View Deployments

```bash
kubectl get deployments
```

---

## Logs

```bash
kubectl logs pod_name
```

---

## Enter Pod

```bash
kubectl exec -it pod_name -- bash
```

Equivalent of:

```bash
docker exec
```

---

# Frequently Asked Kubernetes Interview Questions

## Basic

### What is Kubernetes?

Container orchestration platform used to manage, scale, and deploy containers.

---

### Why Kubernetes if we already have Docker?

Docker creates containers.

Kubernetes manages containers at scale.

---

### What is a Pod?

Smallest deployable unit in Kubernetes.

Usually contains one container.

---

### What is a Deployment?

Object responsible for managing Pods and maintaining desired replica count.

---

### What is a Replica?

Copy of a Pod.

Used for scalability and availability.

---

## Intermediate

### What is a Service?

Provides stable networking endpoint for Pods.

---

### What is Ingress?

Manages incoming HTTP/HTTPS traffic.

---

### What is ConfigMap?

Stores configuration data.

---

### What is Secret?

Stores sensitive information securely.

---

### What is Persistent Volume?

Storage that survives Pod recreation.

---

## Advanced

### What is Self-Healing?

Kubernetes automatically recreates failed Pods.

---

### What is Rolling Update?

Gradual deployment without downtime.

---

### What is Auto Scaling?

Automatic increase/decrease of Pods based on load.

---

### What happens if a Node crashes?

Pods are recreated on another healthy node.

---

### How does Kubernetes achieve high availability?

Using:

* Multiple Nodes
* Multiple Pods
* Services
* Load Balancing

---

# Must-Memorize Senior-Level Questions

1. Why Kubernetes when Docker already exists?
2. What is a Pod?
3. What is a Deployment?
4. What are Replicas?
5. What is a Service?
6. What is Ingress?
7. What is Self-Healing?
8. What are Rolling Updates?
9. How does Auto Scaling work?
10. Explain a production Laravel architecture using Kubernetes.

---

# Interview Answer You Can Use

**"Have you worked with Kubernetes?"**

If your exposure is limited:

> I have primarily worked as a Laravel backend developer and have experience deploying applications using Docker. I understand Kubernetes concepts such as Pods, Deployments, Services, Ingress, auto-scaling, and rolling updates. In a production environment, I understand how Kubernetes helps manage multiple containerized Laravel instances, provides high availability, and automates deployments and recovery. While I am not a dedicated DevOps engineer, I am comfortable working with Kubernetes-based deployments and collaborating with infrastructure teams.

This answer is honest and appropriate for most Senior Laravel positions.

The next technology after Kubernetes should be **Kafka**, because interviewers often ask:
**"How would you process millions of events, notifications, or orders asynchronously?"** That's where Kafka comes in.
