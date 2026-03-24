# Kubernetes Debugging Commands (Logs & Exec)

## 1. Why Debugging is Needed

While working with Kubernetes, issues can occur during:

- Creating pods
- Updating deployments
- Deleting resources
- Running applications

👉 Common problems include:

- Pods not starting
- Containers crashing
- Configuration errors

To debug these issues, Kubernetes provides two important commands:

- **logs**
- **exec**

---

## 2. kubectl Logs Command

### Syntax

```
kubectl logs [POD_NAME]
```

### Purpose

- Displays logs of a specific pod
- Helps identify:
  - Errors
  - Warnings
  - Application issues

---

## 3. kubectl Exec Command

### Syntax

```
kubectl exec -it [POD_NAME] -- /bin/bash
```

### Purpose

- Opens a terminal inside the pod
- Allows you to:
  - Inspect files
  - Run commands
  - Debug interactively

---

## 4. Real Debugging Example (MySQL Crash)

### Step 1: Create Deployment

You created a deployment:

```
mysql-ac-deployment
```

---

### Step 2: Check Pod Status

```
kubectl get pods
```

### Output:

```
NAME                                         READY   STATUS             RESTARTS   AGE
mysql-ac-deployment-778857966d-pr5g2          0/1     CrashLoopBackOff   6          9m42s
```

---

### What is CrashLoopBackOff?

👉 This means:

- Container is starting
- Then crashing repeatedly
- Kubernetes keeps restarting it

---

## 5. Debug Using Logs

```
kubectl logs mysql-ac-deployment-778857966d-pr5g2
```

### Output (Important Error):

```
[ERROR]: Database is uninitialized and password option is not specified
You need to specify one of the following:
- MYSQL_ROOT_PASSWORD
- MYSQL_ALLOW_EMPTY_PASSWORD
- MYSQL_RANDOM_ROOT_PASSWORD
```

---

### Root Cause

- MySQL requires a **root password**
- No environment variable was provided

👉 So the container fails to start

---

## 6. Fix the Issue (Set Environment Variable)

```
kubectl set env deployment/mysql-ac-deployment MYSQL_ROOT_PASSWORD=pass123
```

---

### What This Does

- Adds environment variable to deployment
- Kubernetes:
  - Creates new pod
  - Applies updated configuration

---

### Check Again

```
kubectl get pods
```

### Output:

```
NAME                                         READY   STATUS    RESTARTS   AGE
mysql-ac-deployment-556b4c7d6-857f7           1/1     Running   0          6s
```

👉 Now pod is running successfully

---

## 7. Access Pod Terminal (Exec)

```
kubectl exec -it mysql-ac-deployment-556b4c7d6-857f7 -- /bin/bash
```

### Output:

```
bash-5.1#
```

👉 Now you are inside the container and can:

- Run Linux commands
- Debug manually

---

## 8. Deleting Resources

### Delete Deployment

```
kubectl delete deployment nginx-deployment
```

👉 This deletes:

- Deployment
- ReplicaSets
- Pods (automatically)

---

### Delete Only Pod

```
kubectl delete pod nginx-deployment-64df4c54c7-xcnsz
```

👉 Note:

- If pod belongs to a deployment:
  - It will be **recreated automatically**

---

## 9. View All Resources

```
kubectl get all
```

---

### Example Output

```
NAME                                               READY   STATUS    RESTARTS   AGE
pod/mysql-ac-deployment-556b4c7d6-857f7             1/1     Running   0          11m

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   170m

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mysql-ac-deployment    1/1     1            1           25m

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/mysql-ac-deployment-556b4c7d6     1         1         1       11m
replicaset.apps/mysql-ac-deployment-778857966d    0         0         0       25m
```

---

### Explanation

- **Pod** → Running application
- **Service** → Internal networking
- **Deployment** → Manages pods
- **ReplicaSet** → Maintains replica count

---

## 10. CLI vs YAML (Important Concept)

### Using CLI

We can create deployments using:

```
kubectl create deployment ...
```

👉 But:

- Many options become complex
- Hard to manage configurations

---

### Using YAML (Recommended)

👉 In real-world projects:

- We use **YAML files**
- Benefits:
  - Easy to read
  - Version controlled
  - Reusable
  - Cleaner configuration

---

## 11. Final Summary

- Use **kubectl logs** → to check errors
- Use **kubectl exec** → to access container
- CrashLoopBackOff:
  - Indicates repeated failure
- Fix issues by:
  - Checking logs
  - Updating configuration (like env variables)
- Deleting:
  - Deployment → removes everything
  - Pod → may be recreated automatically
- `kubectl get all`:
  - Shows full cluster resources
- For real-world usage:
  - Prefer **YAML over CLI**

---
