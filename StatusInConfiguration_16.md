# Checking Deployment Status in Kubernetes

## 1. Viewing Deployment Details in YAML

To check the **complete status and configuration** of a deployment:

```
kubectl get deployments nginx-ac-deployment -o yaml
```

👉 This command returns:

- Original configuration (what you applied)
- System-generated fields
- Current runtime status

---

## 2. Understanding the Output

The YAML output contains **three major sections**:

---

## 2.1 Metadata (System + User Data)

```
metadata:
  name: nginx-ac-deployment
  namespace: default
  labels:
    app: nginx-ac
```

---

### Automatically Generated Fields

Kubernetes adds several fields automatically:

- **resourceVersion**
  - Tracks version of the resource
  - Changes whenever the object is updated
- **uid**
  - Unique identifier (UUID)
  - Assigned to each resource
- **creationTimestamp**
  - When the deployment was created
- **annotations**
  - Stores extra metadata
  - Example:
    - Last applied configuration
    - Revision history

👉 These are **not written by the user**, but added by Kubernetes internally.

---

## 2.2 Specification (spec)

This section contains the **desired state**.

```
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-ac
```

---

### Key Fields

- **replicas: 2**
  - Two pods should be running
- **strategy**

```
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%
```

### Meaning:

- **RollingUpdate**:
  - Pods are updated gradually (no downtime)
- **maxSurge (25%)**
  - Extra pods allowed during update
- **maxUnavailable (25%)**
  - Max pods that can be unavailable during update

---

### Pod Template

```
template:
  metadata:
    labels:
      app: nginx-ac
  spec:
    containers:
      - image: nginx:1.24
```

👉 Defines:

- Pod structure
- Container configuration

---

## 2.3 Status (Current State)

```
status:
  replicas: 2
  readyReplicas: 2
  availableReplicas: 2
  updatedReplicas: 2
```

---

### Key Fields

- **replicas**
  - Total desired pods
- **readyReplicas**
  - Pods ready to serve traffic
- **availableReplicas**
  - Pods available for use
- **updatedReplicas**
  - Pods using latest configuration

---

### Conditions

```
conditions:
  - type: Available
    status: True
  - type: Progressing
    status: True
```

### Meaning:

- **Available = True**
  - Deployment is healthy
- **Progressing = True**
  - Deployment updates are successful

---

## 3. Important Concept

👉 The YAML you see here is **NOT the original file you wrote**

It is a **combination of**:

- Your configuration
- Kubernetes-generated data
- Current runtime state

---

## 4. Production Practices

In real-world environments, there are two common approaches:

---

### 1. Configuration with Application Code

- YAML files are stored along with code
- Example:
  - Same repository (monorepo)

👉 Useful for:

- Small projects
- Simple deployments

---

### 2. Separate Configuration Repository

- Infrastructure YAML files stored separately

👉 Common in large organizations:

- GitOps approach
- Better separation of concerns

Example:

- App repo → source code
- Infra repo → Kubernetes YAML

---

## 5. Deleting Resources

To delete resources created from YAML:

---

### Delete Deployment

```
kubectl delete -f nginx-ac-deployment.yaml
```

Output:

```
deployment.apps "nginx-ac-deployment" deleted
```

---

### Delete Service

```
kubectl delete -f nginx-ac-service.yaml
```

Output:

```
service "nginx-ac-service" deleted
```

---

### What Happens Internally?

Deleting a deployment will also delete:

- ReplicaSets
- Pods

👉 Because they are controlled by the deployment

---

## 6. Checking All Resources

```
kubectl get all
```

---

### Example Output

```
NAME                 TYPE        CLUSTER-IP   PORT(S)
service/kubernetes   ClusterIP   10.96.0.1    443/TCP
```

---

### Explanation

- Only default Kubernetes service remains
- Your deployment and service are removed

---

## 7. Final Summary

- Use:
  ```
  kubectl get deployment -o yaml
  ```
  👉 To inspect full configuration and status
- YAML output contains:
  - Metadata (auto + user)
  - Spec (desired state)
  - Status (current state)
- Kubernetes automatically adds:
  - resourceVersion
  - UID
  - timestamps
  - annotations
- Production approaches:
  - Store YAML with code
  - OR use separate infra repository
- Deletion:
  - `kubectl delete -f`
  - Removes all associated resources
