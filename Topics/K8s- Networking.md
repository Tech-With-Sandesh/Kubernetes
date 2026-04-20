# ☸️ Kubernetes Networking Guide (Complete)

---

## 🎯 Overview

This guide explains **how networking works in Kubernetes**, focusing on:

- Pod to Pod communication  
- Service-based communication  
- DNS resolution  
- kube-proxy routing  

---

## 🧠 Core Idea

👉 In Kubernetes, **pods do NOT directly call other pods**  
👉 They communicate using **Services**

---

## 🔁 Pod-to-Pod Communication (Real Flow)

```
Pod A
  ↓
Service Name (DNS)
  ↓
ClusterIP
  ↓
kube-proxy
  ↓
Pod B
```

---

## 🔹 Step-by-Step Explanation

### 1️⃣ Pod Calls Service

Pod A sends request using Service name:

```
http://backend-service
```

👉 Pods never use Pod IP directly

---

### 2️⃣ DNS Resolves Service Name

Kubernetes DNS converts:

```
backend-service → ClusterIP (e.g., 10.96.0.10)
```

👉 This is a **virtual IP**

---

### 3️⃣ kube-proxy Intercepts

- Runs on every node  
- Watches Services and Endpoints  
- Uses iptables / IPVS rules  

👉 It intercepts traffic sent to ClusterIP  

---

### 4️⃣ Forward to Backend Pod

kube-proxy forwards request to one of the pods:

```
Pod A → Service → Pod B1 / Pod B2 / Pod B3
```

👉 Load balancing happens here  

---

## 🔑 Key Components

### 🔹 Pod

- Smallest unit in Kubernetes  
- Runs application  

---

### 🔹 Service

- Stable IP (ClusterIP)  
- Provides DNS name  
- Load balances traffic  

---

### 🔹 kube-proxy

- Network component  
- Routes traffic to pods  
- Uses iptables/IPVS  

---

### 🔹 DNS (CoreDNS)

- Resolves service names  
- Example:

```
backend-service.default.svc.cluster.local
```

---

## 🔄 Full Communication Flow

1. Pod sends request to Service  
2. DNS resolves name → ClusterIP  
3. kube-proxy intercepts request  
4. Request routed to backend pod  
5. Response goes back to source  

---

## 🌐 Service Types

### 1️⃣ ClusterIP (Default)

- Internal communication only  

---

### 2️⃣ NodePort

- Exposes service on node IP  

---

### 3️⃣ LoadBalancer

- Exposes service externally  

---

## ⚡ Important Concepts

### ✔ No Direct Pod Communication

- Pod IP changes frequently  
- Services provide stability  

---

### ✔ Load Balancing

- Handled by kube-proxy  

---

### ✔ Flat Network

- All pods can reach each other  

---

## 🔍 Example YAML (Service)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
```

---

## 🔍 Example YAML (Pod)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: client-pod
spec:
  containers:
    - name: app
      image: curlimages/curl
      command: ["sleep", "3600"]
```

---

## 🔍 Test Communication

```bash
kubectl exec -it client-pod -- curl http://backend-service
```

---

## 🧠 Summary

- Pods communicate using Services  
- DNS resolves Service → ClusterIP  
- kube-proxy routes traffic  
- Service load balances to pods  

---

## 🎯 One-Line Summary

👉 Pod → Service → kube-proxy → Pod

---

## 💡 Memory Trick

Service = Entry  
kube-proxy = Routing  
Pod = Execution  

---
