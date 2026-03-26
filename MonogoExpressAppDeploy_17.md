# Kubernetes Project: MongoDB + Mongo Express

## 1. Application Overview

We are building a simple **2-tier application**:

### Components:

1. **MongoDB**
   - Acts as the **database**
   - Stores all application data
2. **Mongo Express**
   - Acts as the **frontend UI**
   - Allows users to view and manage database data through a browser

---

## 2. How the Application Works

### Flow of Request

```
User → Mongo Express (UI) → MongoDB → Response → UI
```

---

### Detailed Flow

1. User sends request to **Mongo Express UI**
2. Mongo Express:
   - Reads **database URL from ConfigMap**
   - Reads **credentials from Secret**
3. Mongo Express forwards request to **MongoDB**
4. MongoDB processes request
5. Response is sent back to UI
6. UI reflects updated data

---

## 3. Kubernetes Architecture

### Pods

- **Pod 1** → MongoDB
- **Pod 2** → Mongo Express

👉 Each component runs in its **own pod**

---

### Services

We create **2 services**:

1. **MongoDB Service (Internal)**
   - Used for communication inside cluster
   - Not exposed to external users
2. **Mongo Express Service (External)**
   - Exposed to users
   - Accepts incoming requests

---

### Configuration Components

### 1. Secret

Used to store **sensitive data**:

- Database username
- Database password

👉 Both MongoDB and Mongo Express need access to this

---

### 2. ConfigMap

Used to store **non-sensitive configuration**:

- MongoDB connection URL

👉 Mongo Express uses this to connect to MongoDB

---

## 4. Communication Flow in Kubernetes

```
External User
      ↓
Mongo Express Service (External)
      ↓
Mongo Express Pod
      ↓
Reads ConfigMap (DB URL)
Reads Secret (Credentials)
      ↓
MongoDB Service (Internal)
      ↓
MongoDB Pod
      ↓
Database Response
      ↓
Mongo Express → User
```

---

## 5. Important Concepts

### Why Separate Pods?

- Better scalability
- Independent management
- Fault isolation

---

### Why Services?

- Pods have dynamic IPs
- Services provide:
  - Stable access
  - Load balancing

---

### Why Secret?

- Sensitive data should not be hardcoded
- Secure storage for credentials

---

### Why ConfigMap?

- Keeps configuration separate from code
- Easy to update without changing deployment

---

## 6. Deployment Order (Very Important)

👉 Resources must be created in the correct order

---

### Step-by-Step Process

### 1. Create Secret

- Store:
  - MongoDB username
  - MongoDB password

👉 Must be created first because deployments depend on it

---

### 2. Create MongoDB Deployment

- Creates MongoDB pod
- Uses Secret for credentials

---

### 3. Create MongoDB Service (Internal)

- Enables communication inside cluster
- Mongo Express will use this service

---

### 4. Create ConfigMap

- Store MongoDB connection URL
- Used by Mongo Express

---

### 5. Create Mongo Express Deployment

- Creates Mongo Express pod
- Uses:
  - ConfigMap (for DB URL)
  - Secret (for credentials)

---

### 6. Create Mongo Express Service (External)

- Exposes Mongo Express to outside world
- Users access application through this

---

## 7. Important Rule

👉 Always create **Secret and ConfigMap BEFORE Deployments**

Because:

- Pods need them at startup
- If not available → pod will fail

---

## 8. Internal vs External Service

| Service Type     | Usage                         |
| ---------------- | ----------------------------- |
| Internal Service | MongoDB (cluster-only access) |
| External Service | Mongo Express (user access)   |

---

## 9. Final Summary

- Two applications:
  - MongoDB (database)
  - Mongo Express (frontend UI)
- Two pods:
  - One for DB
  - One for UI
- Two services:
  - Internal → MongoDB
  - External → Mongo Express
- Configuration:
  - Secret → credentials
  - ConfigMap → DB URL
- Deployment order:
  1. Secret
  2. MongoDB Deployment
  3. MongoDB Service
  4. ConfigMap
  5. Mongo Express Deployment
  6. Mongo Express Service

---

## 10. Key Takeaway

👉 This architecture represents a **real-world Kubernetes pattern**:

- Separate concerns (DB vs UI)
- Use services for communication
- Use Secret & ConfigMap for configuration
- Follow proper deployment order
