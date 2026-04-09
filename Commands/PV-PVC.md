# ☸️ Kubernetes PV vs PVC (Complete YAML Guide)

---

## 🎯 Overview

This guide explains:

- What is Persistent Volume (PV)
- What is Persistent Volume Claim (PVC)
- How they work together
- Real YAML examples

---

## 🧠 Basic Concept

- **PV (Persistent Volume)** → Actual storage (Disk)
- **PVC (Persistent Volume Claim)** → Request for storage
- **Pod** → Uses PVC (not PV directly)

👉 Flow:

Pod → PVC → PV

---

## 📦 Step 1: Create Persistent Volume (PV)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data
```

---

## 📌 Explanation

- `capacity` → Total storage available  
- `accessModes` → How volume can be accessed  
- `hostPath` → Local path on node (for demo only)  

---

## 📦 Step 2: Create Persistent Volume Claim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

---

## 📌 Explanation

- PVC requests storage from available PV  
- Kubernetes finds matching PV automatically  

---

## 🔗 Step 3: Use PVC in a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - mountPath: "/data"
          name: my-storage
  volumes:
    - name: my-storage
      persistentVolumeClaim:
        claimName: my-pvc
```

---

## 📌 Explanation

- Pod uses PVC (not PV directly)  
- Data is stored in mounted path `/data`  

---

## 🔄 Full Flow

1. PV is created (storage available)  
2. PVC requests storage  
3. Kubernetes binds PVC → PV  
4. Pod uses PVC  
5. Data persists even if pod restarts  

---

## 🔑 Important Concepts

### Access Modes

| Mode | Meaning |
|------|--------|
| ReadWriteOnce | One node can read/write |
| ReadOnlyMany | Multiple nodes read only |
| ReadWriteMany | Multiple nodes read/write |

---

### Reclaim Policy

| Policy | Meaning |
|--------|--------|
| Retain | Keep data after delete |
| Delete | Delete storage |
| Recycle | (Deprecated) |

---

## ⚡ Best Practices

- Use **PVC instead of directly using PV**
- Use **StorageClass for dynamic provisioning**
- Avoid hostPath in production
- Use cloud storage (EBS, Azure Disk, etc.)

---

## 🔥 Bonus: Dynamic Provisioning (Optional)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: standard
  resources:
    requests:
      storage: 1Gi
```

👉 Kubernetes automatically creates PV

---

## ❌ Common Mistakes

- Pod trying to use PV directly  
- No matching PV for PVC  
- Wrong access mode  
- Using hostPath in production  

---

## 🧠 One-Line Summary

👉 PV = Storage  
👉 PVC = Request  
👉 Pod uses PVC  

---
