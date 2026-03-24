# Kubernetes Architecture

## 1. Master–Worker Model

A Kubernetes cluster follows a **Master–Worker architecture**.

### Types of Nodes

There are **two types of nodes** in a cluster:

1. **Master Node (Control Plane)**
2. **Worker Nodes**

---

### Responsibilities

- **Master Node**
    - Makes all the **decisions**
    - Manages the cluster
    - Decides:
        - How to schedule pods
        - When to restart pods
        - Monitoring and logging decisions
        - Rescheduling if something fails
- **Worker Nodes**
    - Execute the decisions made by the master
    - Run actual applications (containers inside pods)

---

### What is a Node?

A **node** is simply a machine:

- Can be a **physical machine**
- Or a **virtual machine**

These machines run:

- Kubernetes components
- Applications (containers)

---

### Cluster Composition

A Kubernetes cluster can have:

- One or multiple **master nodes**
- Multiple **worker nodes**

---

## 2. Master Node (Control Plane)

The **Master Node** is the brain of the cluster.

Inside the master node, we have the **Control Plane**, which is responsible for all decision-making.

---

### Core Components of Master Node

There are **5 components**, but **4 are most critical**:

1. API Server
2. Controller Manager
3. Scheduler
4. ETCD
5. Cloud Controller Manager (optional/external integration)

---

### 2.1 API Server

- The **main entry point** to Kubernetes
- All requests from users/developers go through the API Server

### Responsibilities:

- Accepts incoming requests
- Performs:
    - **Authentication** (who are you?)
    - **Authorization** (what are you allowed to do?)
- Ensures only valid users can access the cluster
- Forwards valid requests to other components

---

### 2.2 Controller Manager

- Receives instructions from the API Server
- Runs multiple **controllers**

### Responsibilities:

- Maintains the **desired state**
- Detects differences between:
    - Desired state vs Actual state

### Example:

- Desired pods = 3
- Actual running = 2
👉 Controller automatically creates 1 more pod

---

### 2.3 Scheduler

- Decides **where (on which node)** a pod should run

### Important Note:

- Scheduler **only makes decisions**
- It does NOT run pods

👉 Actual execution is done by **Kubelet (on worker nodes)**

---

### 2.4 ETCD

- A **key-value database** that stores all cluster data

### Stores:

- Pod information
- Node information
- Configuration data
- Cluster state

### Role:

- Controllers check ETCD to detect state changes
- Acts as the **single source of truth**

---

### 2.5 Cloud Controller Manager

- Used when Kubernetes runs on cloud platforms

### Responsibilities:

- Communicates with cloud providers like:
    - AWS
    - Azure
    - GCP

---

### Control Plane (Multiple Masters)

- If multiple master nodes exist:
    - Each has all core components
- Together they form the **Control Plane**

👉 This improves:

- High availability
- Fault tolerance

---

## 3. Worker Nodes

Worker nodes are responsible for **executing workloads**.

They run:

- Pods
- Containers

---

### Core Components of Worker Node

1. Container Runtime (CRI)
2. Kubelet
3. Kube Proxy

---

### 3.1 Container Runtime (CRI)

- CRI = **Container Runtime Interface**

### Responsibilities:

- Pull container images
- Run containers
- Manage container lifecycle

### Workflow:

- Master decides what to run
- CRI executes it inside the worker node

### Inside Worker Node:

- Pod is created
- Inside pod → container runs using CRI

### Popular Runtimes:

- containerd (default)
- Docker (uses containerd internally)

---

### 3.2 Kubelet

- Runs on every worker node

### Responsibilities:

- Communicates with:
    - API Server
    - Container Runtime (CRI)
- Ensures:
    - Containers are running as expected
    - Pods are properly created and maintained

👉 It acts as a **bridge between control plane and worker node execution**

---

### 3.3 Kube Proxy

- Manages **networking**

### Responsibilities:

- Handles:
    - Network routing
    - Request forwarding
- Ensures communication between:
    - Pods
    - Services

---

## 4. How Everything Works Together

### Flow Summary

1. User sends request → **API Server**
2. API Server validates request
3. Controller Manager checks desired vs actual state
4. Scheduler decides where to run pod
5. Decision sent to worker node
6. Kubelet receives instruction
7. CRI pulls image and runs container
8. Kube Proxy manages networking

---

## 5. Final Summary

- **Master Node (Control Plane)**:
    - Makes decisions
    - Manages cluster state
- **Worker Nodes**:
    - Execute workloads
    - Run containers inside pods
- **Key Components**:
    - API Server → Entry point
    - Controller Manager → Maintains state
    - Scheduler → Chooses node
    - ETCD → Stores data
    - Kubelet → Executes instructions
    - CRI → Runs containers
    - Kube Proxy → Handles networking

---

This is the **complete working architecture of Kubernetes**, covering:

- Decision-making
- Execution
- Networking
- State management