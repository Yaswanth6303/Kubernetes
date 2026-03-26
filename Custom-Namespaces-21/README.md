# Custom Namespaces in Kubernetes

## 1. Creating a Custom Namespace

In Kubernetes, you can create your own namespace using the `kubectl create` command:

```
kubectl create namespace custom-ac-namespace
```

👉 This creates a new namespace where you can organize your resources separately from the default namespace.

---

## 2. Creating Resources in a Specific Namespace

By default, Kubernetes creates resources in the **default namespace**.

If you want to create resources in a custom namespace, you must specify it explicitly.

---

### Using `-namespace` Flag

```
kubectl apply -f nginx-ac-deployment.yaml --namespace=custom-ac-namespace
```

Output:

```
deployment.apps/nginx-ac-deployment created
```

---

## 3. Verifying Resources

### Check Deployments (Default Namespace)

```
kubectl get deployments
```

Output:

```
No resources foundin default namespace.
```

👉 Why?

- Because the deployment was created in **custom-ac-namespace**, not in default.

---

### Check Deployments in Custom Namespace

```
kubectlget deployments -n custom-ac-namespace
```

Output:

```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
nginx-ac-deployment   1/1     1            1           91s
```

👉 Now you can see the deployment.

---

## 4. Listing All Namespaces

```
kubectl get namespaces
```

Output:

```
NAME                   STATUS   AGE
custom-ac-namespace    Active   4m5s
default                Active   2d4h
kube-node-lease        Active   2d4h
kube-public            Active   2d4h
kube-system            Active   2d4h
```

---

## 5. Problem with `-namespace` Flag

In real-world scenarios:

- There can be **many namespaces**
- You may forget:
  - Which namespace you are working in
- Writing `n <namespace>` every time is:
  - Repetitive
  - Error-prone

---

## 6. Better Approach: Define Namespace in YAML

Instead of passing namespace in CLI, define it directly in the YAML file.

---

### Example YAML

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ac-deployment
  namespace: custom-ac-namespace
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

### Benefits

- No need to use `-namespace` every time
- Cleaner and more maintainable
- Preferred in real-world projects

---

## 7. Default Behavior of kubectl

When you run:

```
kubectl get deployments
```

👉 Kubernetes:

- Looks only in the **default namespace**

---

### Example Scenario

If your resources are in:

```
custom-yaswanth-namespace
```

You must run:

```
kubectl get deployments -n custom-yaswanth-namespace
```

---

## 8. Problem with Multiple Namespaces

- If you have many namespaces:
  - You must remember each name
  - You must always use `n` flag

👉 This becomes inefficient in daily usage

---

## 9. Solution: kubens Tool

To simplify namespace switching, we use a tool called **kubens**.

---

### What kubens Does

- Changes the **default namespace context**
- After switching:
  - No need to use `n` flag repeatedly

---

### Example Usage

```
kubens custom-ac-namespace
```

👉 Now all commands will run in this namespace by default:

```
kubectl get deployments
```

✔️ Automatically fetches from `custom-ac-namespace`

---

## 10. Key Concepts Recap

- Namespaces organize resources in Kubernetes
- By default, everything goes to **default namespace**
- Use:
  - `-namespace` flag (CLI method)
  - OR `namespace:` field (YAML method)
- kubectl commands:
  - Default → uses default namespace
  - Use `n` to specify namespace
- kubens:
  - Makes namespace switching easier

---

## 11. Final Summary

- Create namespace:
  ```
  kubectl create namespace <name>
  ```
- Apply resources:
  ```
  kubectl apply -f file.yaml -n <namespace>
  ```
- OR define namespace in YAML:
  ```
  metadata:
    namespace: <name>
  ```
- View resources:
  ```
  kubectl get deployments-n <namespace>
  ```
- Use kubens to simplify workflow:
  ```
  kubens <namespace>
  ```

---

## 12. Key Takeaway

👉 In real-world Kubernetes usage:

- Always organize resources using namespaces
- Prefer YAML-based namespace definition
- Use tools like **kubens** for better productivity
