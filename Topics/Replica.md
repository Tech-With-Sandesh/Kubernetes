# 🚀 Kubernetes Debugging Guide: Deployment Shows 3 Replicas, But Only 1 Pod Running

## 📌 Overview

A common real-world issue:

> Deployment is configured with 3 replicas, but only 1 pod is running.

This means Kubernetes **desired state ≠ actual state**.

---

## 🧠 How This Works

Kubernetes tries to maintain the desired state:

```
replicas: 3 → Scheduler tries to run 3 pods
```

If only 1 pod is running, something is preventing the other pods from running successfully.

---

## 🎯 Goal

Identify:
- Why remaining pods are not running
- Whether they are Pending, Failed, or Crashing
- Fix the root cause

---

## 🔍 Step 1: Check Pod Status

```bash
kubectl get pods
```

Look for:
- Running
- Pending
- CrashLoopBackOff
- Error

---

## 🔍 Step 2: Describe Pods

```bash
kubectl describe pod <pod-name>
```

Check:
- Events section
- Scheduling errors
- Resource issues

---

## ⚠️ Scenario 1: Pods in Pending State

### Causes:
- Insufficient CPU/memory
- NodeSelector mismatch
- Taints without tolerations

### Example errors:
```
0/3 nodes available: insufficient cpu
```

### Fix:
- Scale cluster (add nodes)
- Reduce resource requests
- Fix nodeSelector/affinity

---

## ⚠️ Scenario 2: CrashLoopBackOff

### Causes:
- Application crash
- Wrong configuration
- Missing environment variables
- Failing liveness probe

### Debug:
```bash
kubectl logs <pod-name> --previous
```

### Fix:
- Fix app config
- Adjust memory limits
- Correct startup command

---

## ⚠️ Scenario 3: ImagePullBackOff

### Causes:
- Wrong image name/tag
- Private registry access issue

### Fix:
- Correct image name
- Add imagePullSecrets

---

## ⚠️ Scenario 4: Readiness Probe Failure

### Impact:
- Pod runs but is not considered ready
- Not included in Service endpoints

### Debug:
```bash
kubectl describe pod <pod-name>
```

### Fix:
- Fix health check endpoint
- Increase delay

---

## ⚠️ Scenario 5: Resource Limits Too High

### Problem:
Pods cannot be scheduled due to high requests

```yaml
resources:
  requests:
    cpu: "4"
    memory: "8Gi"
```

### Fix:
- Reduce resource requests
- Add more nodes

---

## ⚠️ Scenario 6: Node Constraints

### Causes:
- nodeSelector mismatch
- Affinity rules too strict

### Fix:
- Relax constraints
- Match node labels

---

## ⚠️ Scenario 7: Quota Limits

### Problem:
Namespace resource quota exceeded

### Check:
```bash
kubectl describe quota
```

### Fix:
- Increase quota
- Reduce usage

---

## ⚠️ Scenario 8: Pod Evictions

### Causes:
- Node memory/disk pressure

### Check:
```bash
kubectl describe node
```

---

## 📊 Common Root Causes

| Issue | Result |
|------|--------|
| Insufficient resources | Pods Pending |
| App crash | CrashLoopBackOff |
| Image issue | ImagePullBackOff |
| Probe failure | Not ready |
| Node constraints | Scheduling failure |
| Quota exceeded | Pod rejected |

---

## 🎯 Final Debugging Checklist

- [ ] Check pod status
- [ ] Describe pods for events
- [ ] Check logs (`--previous`)
- [ ] Verify resource requests
- [ ] Validate probes
- [ ] Check node constraints
- [ ] Verify quotas

---

## 🚀 Best Practices

- Always define resource requests/limits
- Use proper readiness/liveness probes
- Monitor cluster capacity
- Avoid over-restrictive scheduling rules

---

## 📌 Conclusion

When replicas ≠ running pods, the issue is usually:

- Scheduling failure
- Application crash
- Resource limitation

Debugging systematically helps identify the exact root cause quickly 🚀

---

## ⭐ Support

- Star ⭐ the repo  
- Share with others  
- Follow for more DevOps content  
