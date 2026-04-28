# ☸️ Kubernetes Ingress (Complete YAML Guide)

---

## 🎯 Overview

This guide explains how to expose multiple services using **Ingress** with:

- Frontend app  
- Backend API  
- Path-based routing  

---

## 🧠 Architecture

```
User
  ↓
Ingress
  ↓
Services
  ↓
Pods
```

---

## 🌐 URL Routing

```
http://myapp.com/ui   → Frontend Service
http://myapp.com/api  → Backend Service
```

---

## 📦 Step 1: Frontend Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: nginx
          ports:
            - containerPort: 80
```

---

## 📦 Step 2: Backend Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: hashicorp/http-echo
          args:
            - "-text=Hello from Backend"
          ports:
            - containerPort: 5678
```

---

## 🔌 Step 3: Frontend Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 80
```

---

## 🔌 Step 4: Backend Service

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
      targetPort: 5678
```

---

## 🌐 Step 5: Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
    - host: myapp.com
      http:
        paths:
          - path: /ui
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 80
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: backend-service
                port:
                  number: 80
```

---

## ⚙️ Step 6: Apply Everything

```bash
kubectl apply -f .
```

---

## 🌍 Step 7: Test

Add entry in `/etc/hosts`:

```
<INGRESS-IP> myapp.com
```

---

### Test URLs

```
http://myapp.com/ui
http://myapp.com/api
```

---

## 🔑 Key Concepts

- Ingress routes traffic based on URL  
- Uses one domain for multiple services  
- Works with an Ingress Controller (NGINX, etc.)  

---

## ⚡ Important Note

👉 Ingress requires an **Ingress Controller** to work

Example:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

---

## 🧠 Summary

- Service exposes pods internally  
- Ingress exposes services externally  
- One domain → multiple apps  

---

## 🎯 One-Line Summary

👉 Ingress = Smart routing layer for Kubernetes  

---
