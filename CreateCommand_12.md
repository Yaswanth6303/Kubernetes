# kubectl Commands – Creating and Managing Deployments

## 1. Creating Resources in Kubernetes

In Kubernetes, we use the `kubectl create` command to create resources.

### Basic Syntax

```
kubectl create deployment [NAME] --image=[IMAGE] [options]
```

---

## 2. Important Concept: Why We Use Deployments Instead of Pods

Even though **Pods** are the smallest unit in Kubernetes:

👉 We **do NOT create Pods directly in most cases**

### Why?

- Pods are **low-level objects**
- They are not designed to be managed manually
- If a pod crashes, it won’t automatically recover

---

### Solution: Deployment (Abstraction Layer)

We use **Deployment** as a higher-level abstraction.

👉 Deployment:

- Automatically creates Pods
- Manages Pod lifecycle
- Handles:
  - Scaling
  - Updates
  - Self-healing

---

## 3. Creating a Deployment (Example with NGINX)

Let’s create a deployment that runs **NGINX** inside a pod:

```
kubectl create deployment nginx-deployment --image=nginx
```

### Notes:

- This creates a deployment named `nginx-deployment`
- By default, it pulls:
  ```
  nginx:latest
  ```

---

## 4. Checking Deployment Status

```
kubectl get deployments
```

### Example Output:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   0/1     1            0           9s
```

### Explanation:

- **READY (0/1)** → 0 pods ready out of 1 desired
- **UP-TO-DATE** → Number of updated pods
- **AVAILABLE** → Pods available to serve traffic

👉 Sometimes AVAILABLE = 0 initially because:

- Image is being pulled
- Container is starting

After some time:

```
READY = 1/1
AVAILABLE = 1
```

---

## 5. Checking Pods

```
kubectl get pods
```

### Example Output:

```
NAME                                      READY   STATUS    RESTARTS   AGE
nginx-deployment-6ff797d4c9-sqxkl          1/1     Running   0          7m22s
```

### Explanation:

- `6ff797d4c9` → ReplicaSet ID
- `sqxkl` → Unique Pod ID

👉 This shows:

- Pod is running successfully

---

## 6. What Happens Internally? (ReplicaSet)

When you create a Deployment:

👉 Kubernetes automatically creates a **ReplicaSet**

---

### Role of ReplicaSet

- Ensures the correct number of pods are running
- Maintains **replica count**

---

### Important Notes:

- ReplicaSet is:
  - **Automatically created**
  - **Managed by Deployment**
- You typically:
  - Do NOT create it manually
  - Do NOT manage it directly

---

### Check ReplicaSets

```
kubectl get replicasets
```

### Example Output:

```
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-6ff797d4c9   1         1         1       14m
```

### Explanation:

- **DESIRED** → Number of pods expected (default = 1)
- **CURRENT** → Number of pods created
- **READY** → Number of running pods

👉 ReplicaSet gets its state from **ETCD (cluster database)**

---

## 7. Updating a Deployment (Editing Image Version)

To modify a deployment:

```
kubectl edit deployment nginx-deployment
```

---

### What Happens?

- Opens a **YAML configuration file**
- You can edit the container image

---

### Example Change

Before:

```
image: nginx
```

After:

```
image: nginx:1.29.6-alpine3.23
```

---

## 8. Rolling Update Process

After updating the image:

### Step 1: New Pod Creation

```
kubectl get pods
```

```
nginx-deployment-64df4c54c7-xcnsz   0/1   ContainerCreating
nginx-deployment-6ff797d4c9-sqxkl   1/1   Running
```

👉 New pod is being created

👉 Old pod is still running

---

### Step 2: New Pod Becomes Ready

```
nginx-deployment-64df4c54c7-xcnsz   1/1   Running
```

---

### Step 3: Old Pod Removal

- Old pod is terminated automatically
- Kubernetes ensures no downtime

👉 This process is called a **Rolling Update**

---

## 9. ReplicaSet During Update

```
kubectl get replicasets
```

### Example Output:

```
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-64df4c54c7   1         1         1       75s
nginx-deployment-6ff797d4c9   0         0         0       25s
```

---

### Explanation:

- New ReplicaSet:
  - DESIRED = 1 → Active
- Old ReplicaSet:
  - DESIRED = 0 → Scaled down

👉 After some time:

- Old ReplicaSet is removed by **Garbage Collector**

---

## 10. Key Concepts Recap

- **Deployment**
  - High-level abstraction
  - Manages Pods and ReplicaSets
- **Pod**
  - Low-level object (not managed directly)
- **ReplicaSet**
  - Maintains number of replicas
  - Created automatically by Deployment
- **Rolling Updates**
  - New pods created first
  - Old pods removed later
  - Ensures zero downtime

---

## 11. Final Summary

- Use `kubectl create deployment` to create applications
- Deployment automatically:
  - Creates ReplicaSet
  - Creates Pods
- Use:
  - `kubectl get deployments`
  - `kubectl get pods`
  - `kubectl get replicasets`
- Updating deployment:
  - `kubectl edit deployment`
  - Triggers rolling update
- Old resources:
  - Automatically cleaned using Garbage Collector

---
