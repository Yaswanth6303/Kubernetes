# Kubernetes Local Setup

## 1. Single Node Setup (Local Environment)

When working on a **local machine**, we typically use a **single-node Kubernetes setup**.

### What does this mean?

- We only have **one machine**
- This machine acts as:
    - **Master Node (Control Plane)**
    - **Worker Node**

👉 So, everything runs in the **same system**:

- Control plane components
- Worker node components
- Applications (pods & containers)

---

## 2. How It Works

In a local setup:

- The **Control Plane** (API Server, Scheduler, Controller Manager, etc.) runs on the same machine
- The **Worker components** (Kubelet, Container Runtime, Kube Proxy) also run on the same machine

👉 This means:

- There is no separation between master and worker
- It is mainly used for:
    - Learning
    - Development
    - Testing

---

## 3. Tools Required for Local Kubernetes Setup

To achieve this setup, we mainly use two tools:

### 1. Minikube

### 2. kubectl

---

## 4. Minikube

### What is Minikube?

**Minikube** is an **open-source tool** that allows you to run a **single-node Kubernetes cluster locally**.

---

### Key Features of Minikube

- Creates a **local Kubernetes cluster**
- Runs both:
    - Control Plane (Master)
    - Worker Node
- Works on:
    - Virtual Machines
    - Containers (like Docker)

---

### What Minikube Does

- Sets up the entire Kubernetes cluster on your local machine
- Automatically configures:
    - API Server
    - Scheduler
    - Controller Manager
    - ETCD
    - Kubelet
    - Container Runtime

👉 In short, it simulates a **real Kubernetes cluster locally**

---

## 5. kubectl (Kubernetes CLI)

### What is kubectl?

**kubectl** is a **Command Line Interface (CLI)** tool used to interact with a Kubernetes cluster.

---

### Why Do We Need kubectl?

To interact with Kubernetes, we must communicate with the **API Server**.

There are multiple ways to interact with the API Server:

- Kubernetes Dashboard (UI)
- Direct API calls (REST endpoints)
- CLI tools → **kubectl (most commonly used)**

---

### What kubectl Does

- Sends commands to the **API Server**
- Allows us to:
    - Create resources (pods, deployments, services)
    - Delete resources
    - Update configurations
    - View cluster status

---

### Example Operations Using kubectl

- Create a deployment
- Scale applications
- Check running pods
- Debug issues

👉 All these actions are performed by sending requests to the **API Server via kubectl**

---

## 6. Communication Flow

Here’s how interaction works:

1. User runs a command using **kubectl**
2. kubectl sends the request to the **API Server**
3. API Server processes the request
4. Control Plane components take decisions
5. Worker node executes the action

---

## 7. Works Everywhere

One of the biggest advantages of kubectl:

👉 It works with any Kubernetes cluster:

- Local (Minikube)
- Cloud (AWS, Azure, GCP)
- Hybrid environments

You use the **same commands everywhere**

---

## 8. Final Summary

- Local setup uses a **single node**
- That node acts as:
    - Master (Control Plane)
    - Worker
- **Minikube**:
    - Creates and runs the Kubernetes cluster locally
- **kubectl**:
    - CLI tool to interact with the cluster
    - Communicates with API Server

👉 Together:

- Minikube → sets up the cluster
- kubectl → interacts with the cluster