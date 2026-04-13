# 🚀 Kubernetes Debugging Guide: Pods Healthy but Service Returns 503

## 📌 Overview

This is a very common real-world Kubernetes issue:

> Pods are running and appear healthy, but the Service returns **503 Service Unavailable**

This usually indicates a **networking or configuration issue**, not an application crash.

---

## 🧠 What Does 503 Mean in Kubernetes?

A **503 error** typically means:

- Service cannot find healthy backend pods
- Endpoints are empty or incorrect
- Traffic is not reaching the pods

---


## 🎯 Goal

Identify:
- Why Service is not routing traffic
- Where the break is happening

---

## 🔍 Step 1: Check Pod Status

```bash
kubectl get pods
```

Ensure:
- Pods are in `Running` state
- No restarts or crashes

---

## 🔍 Step 2: Check Service Configuration

```bash
kubectl get svc
kubectl describe svc <service-name>
```

Verify:
- Correct `selector`
- Correct `port` and `targetPort`

---

## ⚠️ Step 3: Check Labels and Selectors (Most Common Issue)

### Problem:
Service selectors do not match pod labels → no endpoints created

### Check pod labels:

```bash
kubectl get pods --show-labels
```

### Check service selector:

```bash
kubectl describe svc <service-name>
```

### Fix:
Ensure both match:

```yaml
# Pod
labels:
  app: my-app

# Service
selector:
  app: my-app
```

---

## 🔍 Step 4: Check Endpoints

```bash
kubectl get endpoints <service-name>
```

### If EMPTY:
- Service is not linked to any pod

### Fix:
- Correct labels/selectors

---

## ⚠️ Step 5: Readiness Probe Failure

Even if pods are "Running", they may not be "Ready".

### Check:

```bash
kubectl describe pod <pod-name>
```

Look for:
- Readiness probe failures

### Result:
- Pod is excluded from Service endpoints

### Fix:
- Correct readiness probe config

---

## 🔍 Step 6: Port Mismatch

### Problem:
Service `targetPort` does not match container port

### Example:

```yaml
# Container
ports:
  - containerPort: 8080

# Service (WRONG)
targetPort: 80
```

### Fix:
Match ports correctly:

```yaml
targetPort: 8080
```

---

## 🔍 Step 7: Check Application Binding

### Problem:
App inside container is not listening on expected port

### Check logs:

```bash
kubectl logs <pod-name>
```

### Fix:
- Ensure app binds to `0.0.0.0`, not `localhost`

---

## 🔍 Step 8: Network Policies

### Problem:
Traffic is blocked by NetworkPolicy

### Check:

```bash
kubectl get networkpolicy
```

### Fix:
- Allow traffic from service/namespace

---

## 🔍 Step 9: Ingress Issues (If using Ingress)

### Problem:
Ingress routes to wrong service or path

### Check:

```bash
kubectl describe ingress
```

### Fix:
- Verify backend service name and port

---

## 🔍 Step 10: DNS Issues

### Test from another pod:

```bash
kubectl run test --rm -it --image=busybox -- sh
```

Inside pod:

```bash
wget -qO- http://<service-name>
```

If fails:
- DNS or service issue

---

## 🔍 Step 11: Check Kube Proxy / CNI

Rare but possible.

Check:
- kube-proxy logs
- CNI plugin (Calico, Flannel)

---

## 📊 Common Root Causes

| Issue | Impact |
|------|--------|
| Label mismatch | No endpoints |
| Readiness probe failure | Pod not ready |
| Port mismatch | Traffic failure |
| App binding issue | No response |
| Network policy | Traffic blocked |
| Ingress misconfig | Routing failure |

---

## 🎯 Final Debugging Checklist

- [ ] Pods are running
- [ ] Labels match selectors
- [ ] Endpoints are populated
- [ ] Readiness probe passing
- [ ] Ports are correct
- [ ] App is listening properly
- [ ] No network policy blocking
- [ ] Ingress is correct

---

## 🚀 Best Practices

- Use consistent labels
- Always define readiness probes
- Use meaningful service names
- Validate endpoints after deployment
- Monitor service health

---

## 📌 Conclusion

A 503 error in Kubernetes is usually NOT an app issue.

It is caused by:
- Misconfigured Service
- Incorrect labels
- Readiness failures
- Networking issues

Once you understand Service → Endpoint → Pod flow, debugging becomes easy 🚀

---

## ⭐ If this helped

- Star ⭐ the repo  
- Share with others  
- Follow for more DevOps content  

---
