# Kubernetes YAML Configuration (Detailed Explanation)

## 1. Basic YAML Example (Deployment)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ac-deployment
  labels:
    app: nginx-ac
spec:
  replicas: 2
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
            - containerPort: 5000
```

---

## 2. Main Components of a YAML File

A Kubernetes YAML configuration is mainly divided into **three components**:

---

### 1. Metadata (Data About Data)

```
metadata:
  name: nginx-ac-deployment
  labels:
    app: nginx-ac
```

### Purpose:

- Contains identifying information about the resource

### Includes:

- Name of the resource
- Labels (very important)
- Can also include:
  - ConfigMaps
  - Secrets
  - Other metadata-related info

👉 **Labels** are key-value pairs used to identify and connect resources.

---

### 2. Specification (spec)

```
spec:
  replicas: 2
  ...
```

### Purpose:

- Defines **what Kubernetes should create**

### Includes:

- Number of replicas (pods)
- Container details:
  - Image
  - Ports
- Pod configuration

👉 Everything related to **how the application should run** is defined here.

---

### 3. Status (Automatically Managed)

- Contains:
  - Desired state
  - Current state

👉 Example:

- Desired pods = 2
- Running pods = 2

⚠️ Important:

- You **do NOT define this manually**
- Kubernetes automatically maintains it

---

## 3. Kubernetes Resource Hierarchy

The internal hierarchy is:

```
Deployment → ReplicaSet → Pod → Container
```

---

### Explanation:

- **Deployment**
  - High-level controller
- **ReplicaSet**
  - Ensures correct number of pods
- **Pod**
  - Runs containers
- **Container**
  - Runs actual application

---

## 4. Template (Pod Blueprint)

Inside `spec`, we define a **template**:

```
template:
  metadata:
    labels:
      app: nginx-ac
  spec:
    containers:
      ...
```

---

### Key Concept:

👉 Template = **Blueprint of the Pod**

- Defines how pods should be created
- Contains its own:
  - Metadata
  - Spec

---

## 5. Labels and Selectors (Core Concept)

This is one of the most important concepts in Kubernetes.

---

### What are Labels?

```
labels:
  app: nginx-ac
```

- Provide **identity** to resources
- Used to group and connect resources

---

### What are Selectors?

```
selector:
  matchLabels:
    app: nginx-ac
```

- Used to **find matching resources**
- Match labels of pods

---

### How Connection Works

1. Deployment defines selector:

   ```
   matchLabels:
     app: nginx-ac
   ```

2. Pod (template) has label:

   ```
   labels:
     app: nginx-ac
   ```

👉 Kubernetes matches them:

- Deployment knows:
  👉 “These pods belong to me”

---

### Important Points

- Deployment label → identity of deployment
- Template label → identity of pods
- Selector → connects deployment to pods

👉 Without labels & selectors:

- Resources cannot connect

---

## 6. Multiple Pods

Since:

```
replicas: 2
```

👉 Two pods will be created:

```
kubectlget pods
```

Example:

```
nginx-ac-deployment-xxxxx-abcde   Running   10.244.0.6
nginx-ac-deployment-xxxxx-fghij   Running   10.244.0.7
```

---

## 7. Creating a Service

To expose the deployment, we create a **Service**:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-ac-service
spec:
  selector:
    app: nginx-ac
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
```

---

## 8. Understanding Service Configuration

### selector

```
selector:
  app: nginx-ac
```

👉 Service finds pods with this label

---

### ports

```
port: 80
targetPort: 5000
```

### Meaning:

- **port (80)**:
  - Entry point of the service
  - Where requests come in
- **targetPort (5000)**:
  - Port inside the container (pod)

---

### Default Protocol

- Kubernetes uses **TCP** by default

---

## 9. How Traffic Flows

### Important Concept

👉 Requests do NOT go directly to pods

Instead:

```
Client → Service → Pod
```

---

### Flow Explained

1. Request comes to:

   ```
   Service: Port 80
   ```

2. Service forwards to:

   ```
   Pod IP:5000
   ```

👉 Example:

```
Service → 10.244.0.6:5000
Service → 10.244.0.7:5000
```

---

## 10. Verifying Service

```
kubectl describe service nginx-ac-service
```

### Output:

```
Selector: app=nginx-ac
Type: ClusterIP
IP: 10.97.22.27
Port: 80/TCP
TargetPort: 5000/TCP
Endpoints: 10.244.0.6:5000,10.244.0.7:5000
```

---

### Key Observations

- Service IP → `10.97.22.27`
- Endpoints → Pod IPs:
  - `10.244.0.6:5000`
  - `10.244.0.7:5000`

👉 This proves:

- Service is connected to both pods

---

## 11. Verifying Pod Details

```
kubectlget pods-o wide
```

### Output:

```
NAME                                   READY   STATUS    IP
nginx-ac-deployment-...-8cn68           1/1     Running   10.244.0.6
nginx-ac-deployment-...-kgt9g           1/1     Running   10.244.0.7
```

👉 Matches service endpoints perfectly

---

## 12. Key Concepts Recap

- YAML has:
  - Metadata
  - Spec
  - Status (auto-managed)
- Template:
  - Defines pod configuration
- Labels:
  - Identity of resources
- Selectors:
  - Connect resources
- Flow:
  ```
  Deployment → ReplicaSet → Pod → Container
  ```
- Service:
  - Acts as entry point
  - Routes traffic to pods

---

## 13. Final Summary

- Use YAML to define Kubernetes resources
- Deployment manages pods via:
  - Labels & selectors
- Service connects to pods using labels
- Traffic always flows:
  👉 Client → Service → Pod
- Replicas define number of pods
- Kubernetes automatically:
  - Maintains state
  - Connects resources
  - Balances traffic
