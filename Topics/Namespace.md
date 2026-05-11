# ☸️ Kubernetes Namespace Guide

---

## 🎯 Overview

This guide explains:

- What is a Namespace
- Why Namespaces are used
- How Namespaces work
- Commands and YAML examples

---

## 🧠 What is a Namespace?

A Namespace is a logical separation inside a Kubernetes cluster.

👉 It helps organize and isolate resources.

---

## 🔄 Without Namespace

```
Cluster
 ├── Pod-A
 ├── Pod-B
 ├── Service-A
 ├── Service-B
```

👉 Everything exists together  
👉 Hard to manage

---

## ✅ With Namespace

```
Cluster
 ├── dev
 │    ├── Pod-A
 │    └── Service-A
 │
 └── prod
      ├── Pod-B
      └── Service-B
```

👉 Resources are separated logically

---

## 🔑 Why Use Namespaces?

### ✔ Organization

Separate applications or teams

---

### ✔ Isolation

Resources in one namespace do not affect another

---

### ✔ Access Control

Apply RBAC permissions namespace-wise

---

### ✔ Resource Limits

Control CPU/Memory usage per namespace

---

## 📦 Default Namespaces

| Namespace | Purpose |
|----------|---------|
| default | Default namespace |
| kube-system | Kubernetes system components |
| kube-public | Publicly accessible resources |
| kube-node-lease | Node heartbeat info |

---

## 🚀 Create Namespace

```bash
kubectl create namespace dev
```

---

## 🔍 List Namespaces

```bash
kubectl get namespaces
```

---

## 📦 Create Pod in Namespace

```bash
kubectl run nginx \
  --image=nginx \
  --namespace=dev
```

---

## 🔍 View Resources in Namespace

```bash
kubectl get pods -n dev
```

---

## 🗑 Delete Namespace

```bash
kubectl delete namespace dev
```

👉 Deletes all resources inside it

---

# 📄 Namespace YAML Example

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

---

## 🚀 Apply YAML

```bash
kubectl apply -f namespace.yaml
```

---

# 📄 Deployment in Namespace

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
```

---

## 🌐 Service in Namespace

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: dev
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

---

## 🔗 Namespace Communication

Pods inside same namespace:

```bash
http://nginx-service
```

Across namespaces:

```bash
http://nginx-service.dev.svc.cluster.local
```

---

## ⚡ Resource Quota Example

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "2"
    requests.memory: 4Gi
```

---

## 🔐 RBAC Example

Grant access only to dev namespace.

👉 Useful for team isolation

---

## ❌ Common Mistakes

- Forgetting namespace flag (`-n`)
- Deploying everything in default namespace
- Namespace mismatch in services

---

## 🧠 Best Practices

- Separate dev/prod namespaces
- Use namespaces per team/application
- Apply resource quotas
- Use RBAC with namespaces

---

## 🎯 One-Line Summary

👉 Namespace helps organize and isolate resources inside a Kubernetes cluster.

---

## 💡 Memory Trick

Namespace = Folder inside cluster

---
