# Kubernetes Project: MongoDB + Mongo Express (Complete Guide)

## 1. Architecture Overview

We are building a **2-tier application**:

### Components:

1. **MongoDB**
   - Database (stores data)
   - Runs inside its own pod
2. **Mongo Express**
   - Web UI (frontend)
   - Used to interact with MongoDB via browser

---

## 2. Complete Flow of Application

```
User → Mongo Express (External Service) → Mongo Express Pod
     → ConfigMap (DB URL)
     → Secret (Credentials)
     → MongoDB Service (Internal)
     → MongoDB Pod → Database
```

---

## 3. Important Kubernetes Components Used

| Component  | Purpose                              |
| ---------- | ------------------------------------ |
| Deployment | Creates and manages pods             |
| Service    | Enables communication                |
| Secret     | Stores sensitive data (passwords)    |
| ConfigMap  | Stores non-sensitive config (DB URL) |

---

## 4. Correct Deployment Order (VERY IMPORTANT)

👉 Always follow this order:

1. **Secret**
2. **MongoDB Deployment**
3. **MongoDB Internal Service**
4. **ConfigMap**
5. **Mongo Express Deployment**
6. **Mongo Express External Service**

---

# 5. Step-by-Step Implementation

---

## Step 1: Create Secret (MongoDB Credentials)

```
kubectl apply -f mongodb-ac-secret.yaml
```

Output:

```
secret/mongodb-ac-secret created
```

Check:

```
kubectl get secrets
```

```
NAME                TYPE     DATA   AGE
mongodb-ac-secret   Opaque   2      31m
```

👉 Secret stores:

- Username
- Password

---

## Step 2: Create MongoDB Deployment

```
kubectl apply -f mongodb-ac-deployment.yaml
```

Check:

```
kubectl get all
```

Initial state:

```
pod/mongodb-ac-deployment-xxxxx   0/1   ContainerCreating
```

👉 It takes time to pull image and start container

---

## Step 3: Debug Pod (if needed)

```
kubectl describe pod <pod-name>
```

👉 Useful for:

- Errors
- Events
- Startup issues

---

## Step 4: Create MongoDB Internal Service

👉 Usually, **Deployment + Service are created together**

Apply again:

```
kubectl apply -f mongodb-ac-deployment.yaml
```

Output:

```
service/mongodb-ac-service created
```

---

### Check Service

```
kubectl describe service mongodb-ac-service
```

Output:

- Service Type → `ClusterIP` (internal)
- Port → `27017`
- Endpoints → Pod IP

Example:

```
IP:10.106.102.129
Endpoints:10.244.0.17:27017
```

---

### Verify Pod IP

```
kubectl get pods -o wide
```

```
IP:10.244.0.17
```

👉 Matches service endpoint → Correct connection

---

## Step 5: Rename YAML (Best Practice)

Instead of separate files:

👉 Combine Deployment + Service

Rename the file

```
mongodb-ac-deployment.yaml → mongodb-ac.yaml
```

Then:

```
kubectl delete -f mongodb-ac-deployment.yaml
kubectl apply -f mongodb-ac.yaml
```

---

## Step 6: Create ConfigMap

```
kubectl apply -f mongo-configmap.yaml
```

Output:

```
configmap/mongodb-ac-configmap created
```

👉 Stores:

- MongoDB connection URL

---

## Step 7: Create Mongo Express Deployment

```
kubectl apply -f mongo-express.yaml
```

Output:

```
deployment.apps/mongo-express-ac-deployment created
```

---

## Step 8: Create Mongo Express External Service

```
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-ac-service
spec:
  selector:
    app: mongo-express-ac
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
```

---

## 6. Understanding Service Ports

| Field      | Meaning                       |
| ---------- | ----------------------------- |
| port       | Service port (inside cluster) |
| targetPort | Container port                |
| nodePort   | External access port          |

---

### Flow:

```
User → NodeIP:30000 → Service:8081 → Pod:8081
```

---

## 7. External IP in Minikube

Check services:

```
kubectl get services
```

```
mongo-express-ac-service   LoadBalancer   <pending>
```

👉 In Minikube:

- External IP is **not assigned automatically**

---

### Fix: Use Minikube Service

```
minikube service mongo-express-ac-service
```

Output:

```
http://192.168.49.2:30000
```

OR

```
http://127.0.0.1:59946
```

👉 Browser opens automatically

---

## 8. Login Details

- **Username:** admin
- **Password:** pass

👉 Mongo Express is now connected to MongoDB

---

## 9. Final Request Flow (Complete)

1. User opens:

   ```
   http://192.168.49.2:30000
   ```

2. Request goes to:
   - NodePort `30000`
3. Forwarded to:
   - Service → `8081`
4. Mongo Express Pod:
   - Reads ConfigMap → DB URL
   - Reads Secret → Username & Password
5. Sends request to:
   - MongoDB Service (internal)
6. MongoDB Pod:
   - Processes request
   - Returns data
7. Mongo Express UI:
   - Displays data in browser

---

## 10. Important Notes

- External Service → Mongo Express
- Internal Service → MongoDB
- Secrets → Credentials
- ConfigMap → DB URL
- Pods are connected via:
  👉 **Labels & Selectors**

---

## 11. Key Takeaways

- Always create:
  - Secret & ConfigMap **before deployments**
- Use:
  - Internal service for database
  - External service for UI
- In Minikube:
  - Use `minikube service` for external access
- YAML files:
  - Can combine multiple resources

---

## 12. Real-World Insight

👉 This architecture is very close to production systems:

- Frontend → Backend → Database
- Secure configs via secrets
- Service-based communication
- Decoupled components

---

## 13. Reference from Your Setup Logs

The steps and outputs described above are based on your actual setup and commands , including:

- Secret creation
- Deployment states
- Service endpoints
- NodePort access
- Minikube tunneling
