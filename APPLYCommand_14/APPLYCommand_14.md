# Kubernetes apply Command (Using YAML Configuration)

## 1. Why Use YAML Instead of CLI?

So far, we created resources using **kubectl commands (CLI)**.

👉 But in real-world scenarios:

- CLI becomes **complex and hard to manage**
- Difficult to:
  - Track changes
  - Reuse configurations
  - Maintain large setups

---

### Solution: YAML Configuration Files

- Kubernetes resources are defined using **YAML files**
- These files describe the **desired state** of the system

👉 Then Kubernetes ensures the actual state matches it

---

## 2. `kubectl apply` Command

### Syntax

```
kubectl apply -f [FILE_NAME]
```

### Purpose

- Creates or updates resources from a YAML file
- Works for:
  - Deployments
  - Services
  - Pods
  - ConfigMaps, etc.

---

## 3. Example: Deployment YAML File

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ac-deployment
  labels:
    app: nginx-ac
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-ac
  template:
    metadata:
      labels:
        app: nginx-ac
    spec:
      containers:
        - name: nginx-ac
          image: nginx:1.24
          ports:
            - containerPort: 80
```

---

## 4. Understanding the YAML Structure

### apiVersion

```
apiVersion: apps/v1
```

- Specifies the Kubernetes API version

---

### kind

```
kind: Deployment
```

- Defines the resource type (Deployment)

---

### metadata

```
metadata:
  name: nginx-ac-deployment
  labels:
    app: nginx-ac
```

- Name of the deployment
- Labels used for identification

---

### spec (Desired State)

```
spec:
  replicas: 1
```

- Number of pods to run

---

### selector

```
selector:
  matchLabels:
    app: nginx-ac
```

- Tells Deployment which pods it should manage

---

### template (Pod Blueprint)

```
template:
  metadata:
    labels:
      app: nginx-ac
```

- Labels assigned to pods

---

### container definition

```
containers:
  - name: nginx-ac
    image: nginx:1.24
```

- Defines container:
  - Name
  - Image version

---

### ports

```
ports:
  - containerPort: 80
```

- Exposes port inside the container

---

## 5. Applying the YAML File

```
kubectl apply -f nginx-ac-deployment.yaml
```

👉 This will:

- Create the deployment
- Automatically create:
  - ReplicaSet
  - Pods

---

## 6. Updating the Deployment (Scaling Example)

### Step 1: Edit YAML File

Change:

```
replicas: 1
```

To:

```
replicas: 2
```

---

### Step 2: Apply Again

```
kubectl apply -f nginx-ac-deployment.yaml
```

👉 Kubernetes detects the change and updates the cluster

---

## 7. Verifying Changes

### Check Deployments

```
kubectl get deployments
```

```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
nginx-ac-deployment   2/2     2            2           2m27s
```

---

### Check Pods

```
kubectlget pods
```

```
NAME                                      READY   STATUS    RESTARTS   AGE
nginx-ac-deployment-5d6d56d75f-mdjvb       1/1     Running   0          2m36s
nginx-ac-deployment-5d6d56d75f-r8nj9       1/1     Running   0          12s
```

👉 Two pods are running successfully

---

### Check ReplicaSets

```
kubectl get replicasets
```

```
NAME                          DESIRED   CURRENT   READY   AGE
nginx-ac-deployment-5d6d56d75f 2        2         2       3m24s
```

---

## 8. Key Concept: Declarative Approach

Using `kubectl apply` follows a **declarative model**:

- You define:
  👉 “What you want” (desired state)
- Kubernetes ensures:
  👉 “What is running matches it”

---

## 9. Deleting Resources Using YAML

```
kubectl delete -f nginx-ac-deployment.yaml
```

👉 This deletes:

- Deployment
- ReplicaSet
- Pods (automatically)

---

## 10. What Can Be Created Using YAML?

Using YAML files, you can create:

- Deployments
- Services
- Pods
- ConfigMaps
- Secrets
- StatefulSets

---

## 11. Final Summary

- `kubectl apply -f`:
  - Creates or updates resources from YAML
- YAML files:
  - Define desired state
  - Are reusable and version-controlled
- Updating:
  - Modify YAML
  - Reapply using `kubectl apply`
- Scaling:
  - Change `replicas` value
- Deleting:
  - `kubectl delete -f`

👉 YAML-based approach is the **standard method used in real-world Kubernetes environments**
