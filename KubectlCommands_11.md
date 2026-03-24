# Minikube & kubectl Commands

## 1. Minikube Commands

Once you install Minikube, you can use the following commands to manage your **local Kubernetes cluster**:

---

### Common Minikube Commands

- **Start the cluster**
  ```
  minikube start
  ```
  👉 Creates and starts your local Kubernetes cluster

---

- **Check cluster status**
  ```
  minikube status
  ```
  👉 Shows whether components like:
  - Host
  - Kubelet
  - API Server
    are running or not

---

- **Pause the cluster**
  ```
  minikube pause
  ```
  👉 Temporarily pauses the cluster without deleting it

---

- **Stop the cluster**
  ```
  minikube stop
  ```
  👉 Stops the cluster but keeps the configuration intact

---

- **Delete the cluster**
  ```
  minikube delete
  ```
  👉 Completely removes the cluster and all configurations

---

## 2. After Cluster Creation

Once your cluster is created using Minikube, you need a way to **interact with it**.

👉 This is where **kubectl** comes into the picture.

---

# kubectl Commands

## 3. `kubectl get` Command

The `get` command is used to **retrieve information about resources** in the cluster.

---

### Common Usage

- **Get nodes**
  ```
  kubectl get nodes
  ```
  👉 Displays all nodes in the cluster

---

- **Get pods**
  ```
  kubectl get pods
  ```
  👉 Shows all running pods

---

- **Get services**
  ```
  kubectl get services
  ```
  👉 Displays all services in the cluster

---

### Default Service

- Kubernetes automatically creates a default service called:
  👉 **ClusterIP**
- This service:
  - Is internal to the cluster
  - Used for communication between components

---

## 4. Singular vs Plural Usage

kubectl supports both **singular and plural forms**:

| Resource Type | Singular | Plural   |
| ------------- | -------- | -------- |
| Node          | node     | nodes    |
| Pod           | pod      | pods     |
| Service       | service  | services |

---

### Behavior

- **Plural (recommended)**
  ```
  kubectl get pods
  ```
  👉 Lists **all pods**

---

- **Singular**
  ```
  kubectl get pod <pod-name>
  ```
  👉 Gets details of a **specific pod**

---

## 5. Help Command

- To get help for kubectl commands:
  ```
  kubectl get -h
  ```

👉 This shows:

- Available options
- Command usage
- Examples

---

## 6. Final Summary

- **Minikube commands**:
  - Used to **create and manage the local cluster**
- **kubectl commands**:
  - Used to **interact with the cluster**
- `kubectl get`:
  - Retrieves information about:
    - Nodes
    - Pods
    - Services
- Supports:
  - Singular → specific resource
  - Plural → all resources
