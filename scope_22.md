# Kubernetes Namespace Scope

## 1. What is Scope in Kubernetes?

**Scope** defines **where a resource can be accessed and used**.

In Kubernetes, resources are divided into:

1. **Namespaced resources** → Limited to a specific namespace
2. **Cluster-wide (global) resources** → Accessible across the entire cluster

---

## 2. Example Scenario

Let’s consider:

- One Kubernetes **cluster**
- Two namespaces:
  - `namespace-1`
  - `namespace-2`

---

### Setup

- A **database pod** exists in the cluster
- In `namespace-1` → one application component
- In `namespace-2` → another application component

---

### ConfigMap Example

- A **ConfigMap** is created in `namespace-1`
- It contains:
  - Database URL
  - Username
  - Password

---

## 3. Important Rule: Namespace Isolation

👉 Resources created in one namespace **cannot be directly accessed by another namespace**

---

### What You Might Think

- `namespace-2` can use ConfigMap from `namespace-1`
- It can reuse:
  - Database URL
  - Credentials

---

### Reality in Kubernetes

❌ This is **NOT allowed**

---

### Why?

Because:

- ConfigMap is **namespaced**
- Its scope is **limited to its own namespace**

---

### Correct Behavior

- ConfigMap in `namespace-1`:
  - Can only be used by:
    - Pods
    - Deployments
    - Services
      inside **namespace-1 only**

---

### Same Rule Applies To:

- Secrets
- Services
- Pods
- Deployments
- ConfigMaps

👉 All are **isolated within their namespace**

---

## 4. Key Concept: Namespace Isolation

```
Namespace-1 → Own resources only
Namespace-2 → Own resources only
```

👉 No direct sharing between namespaces

---

## 5. How to Handle This in Real Projects

If multiple namespaces need access:

- Create **separate ConfigMaps/Secrets** in each namespace
  OR
- Use external systems (like external DB, secret managers)

---

## 6. Cluster-Wide (Global) Resources

Some resources are **not limited to namespaces**.

👉 These are called **cluster-scoped resources**

---

### Examples:

- Nodes
- PersistentVolumes (PV)
- StorageClasses

👉 These can be accessed across all namespaces

---

## 7. Listing Namespaced Resources

To check which resources are namespaced:

```
kubectl api-resources--namespaced=true
```

---

### Example Output (Important Resources)

```
configmaps
secrets
pods
services
deployments
replicasets
statefulsets
jobs
cronjobs
ingresses
networkpolicies
roles
rolebindings
```

---

## 8. Meaning of Output

Each row shows:

- Resource name
- API version
- Whether it is namespaced (`true`)
- Resource type

---

### Important Insight

👉 All these resources:

- Belong to a **specific namespace**
- Cannot be accessed outside it

---

## 9. Summary of Scope Types

### 1. Namespaced Resources

Resource Type

---

Pod

---

Service

---

Deployment

---

ConfigMap

---

Secret

---

ReplicaSet

---

StatefulSet

---

👉 Scope:

- Limited to a single namespace

---

### 2. Cluster-Scoped Resources

Resource Type

---

Node

---

PersistentVolume

---

StorageClass

---

👉 Scope:

- Available across entire cluster

---

## 10. Final Summary

- Kubernetes enforces **strict namespace isolation**
- Resources like:
  - ConfigMaps
  - Secrets
  - Pods
  - Services
    are **not shared across namespaces**

---

### Key Rule

👉 A resource in one namespace:

- ❌ Cannot be accessed directly from another namespace
- ✅ Must be recreated or managed separately

---

### Why This Matters

- Improves **security**
- Prevents **accidental access**
- Ensures **clean separation of applications**

---

## 11. Key Takeaway

👉 Namespace scope is a **fundamental concept in Kubernetes**:

- Keeps resources isolated
- Ensures controlled access
- Helps manage large-scale systems
