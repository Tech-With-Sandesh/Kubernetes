# 🚀 Kubernetes Networking Debugging Guide: Pod → Service Works, But Service → Pod Fails

## 📌 Overview

A tricky real-world Kubernetes networking issue:

> Pod can successfully call a Service,  
> but requests through the Service fail to reach the Pod.

This usually indicates a **Service-to-Pod routing issue**, not a basic connectivity problem.

---

## 🧠 Key Concept

Kubernetes Service is an abstraction that routes traffic to Pods using:

- Labels & selectors  
- Endpoints  
- kube-proxy (iptables/ipvs)  
- Cluster networking (CNI)

If Service → Pod fails, the issue lies in this chain.

---

## 🎯 Goal

Identify:
- Whether Service is correctly mapped to Pods  
- Whether traffic is reaching endpoints  
- Whether networking rules are blocking traffic  

---

## 🔍 Step 1: Verify Pod Connectivity

Test from inside another pod:

```bash
kubectl exec -it <pod> -- curl <service-name>
```

If this works → DNS and basic routing are fine.

---

## 🔍 Step 2: Check Service Configuration

```bash
kubectl get svc
kubectl describe svc <service-name>
```

### Verify:
- Selector matches pod labels  
- Correct port and targetPort  

---

## ⚠️ Step 3: Check Endpoints (MOST IMPORTANT)

```bash
kubectl get endpoints <service-name>
```

### Problem:
- No endpoints → Service cannot route traffic  

### Causes:
- Label mismatch  
- Pod not ready  

### Fix:
- Align labels and selectors  
- Fix readiness probe  

---

## ⚠️ Step 4: Readiness Probe Failure

Even if Pod is running:

- If NOT ready → excluded from endpoints  

### Debug:
```bash
kubectl describe pod <pod-name>
```

### Fix:
- Correct readiness probe endpoint  
- Increase delay  

---

## 🔍 Step 5: Port Mismatch

### Problem:
Service `targetPort` does not match container port  

### Example:
```yaml
# Pod
containerPort: 8080

# Service (wrong)
targetPort: 80
```

### Fix:
- Match ports correctly  

---

## 🔍 Step 6: NetworkPolicy Blocking Traffic

### Problem:
Traffic blocked by NetworkPolicy  

### Check:
```bash
kubectl get networkpolicy
```

### Fix:
- Allow traffic between pods/services  

---

## 🔍 Step 7: kube-proxy / CNI Issues

### Problem:
- iptables rules not updated  
- CNI plugin issue (Calico, Flannel, etc.)

### Check:
- kube-proxy logs  
- CNI status  

---

## 🔍 Step 8: Application Binding Issue

### Problem:
App listens only on `localhost`

### Fix:
Bind app to:
```
0.0.0.0
```

---

## 🔍 Step 9: DNS vs Direct Pod Access

Test both:

```bash
# Service
curl http://service-name

# Pod IP
curl http://<pod-ip>
```

### If Pod IP works but Service fails:
→ Service configuration issue  

---

## 📊 Common Root Causes

| Issue | Impact |
|------|--------|
| Label mismatch | No endpoints |
| Readiness probe failing | Pod excluded |
| Port mismatch | Traffic failure |
| NetworkPolicy | Traffic blocked |
| kube-proxy issue | Routing broken |
| App binding | No response |

---

## 🎯 Final Debugging Checklist

- [ ] Service selector matches labels  
- [ ] Endpoints are populated  
- [ ] Pods are ready  
- [ ] Ports match correctly  
- [ ] NetworkPolicy allows traffic  
- [ ] App is listening on correct interface  
- [ ] kube-proxy is working  

---

## 🚀 Best Practices

- Always validate endpoints after deployment  
- Use readiness probes properly  
- Keep labels consistent  
- Monitor Service health  

---

## 📌 Conclusion

If Pod → Service works but Service → Pod fails:

👉 The issue is usually:
- Service configuration  
- Endpoints  
- Readiness  
- Networking rules  

Understanding Service → Endpoint → Pod flow is key 🚀

---

## ⭐ Support

- Star ⭐ the repo  
- Share with others  
- Follow for more DevOps content  

---
