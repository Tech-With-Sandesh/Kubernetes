# 🚀 Kubernetes Interview Questions (Real-World + Deep Answers) – Top 30

## 📌 Overview
This guide contains **30 real-world Kubernetes interview questions with deep, practical answers**.  
Focus is on **debugging, production issues, and real DevOps thinking**.

---

## 🧠 1. Pod is running but app is not accessible… why?

A pod being in `Running` state only means the container is started, not that the application is reachable.

### Possible causes:
- No Service exposing the pod
- Service selector mismatch
- Wrong container port
- Application listening on `localhost` instead of `0.0.0.0`
- NetworkPolicy blocking traffic

### Debugging steps:
```bash
kubectl get pods -o wide
kubectl get svc
kubectl get endpoints <service-name>
kubectl logs <pod-name>
```

### What to verify:
- Service selector matches pod labels
- Endpoints are populated
- Application is listening on correct port

### Fix:
- Update Service selector
- Bind app to `0.0.0.0`
- Correct port mapping

---

## 🧠 2. Pods are healthy but Service returns 503… what’s wrong?

503 means the Service has **no ready endpoints**.

### Root causes:
- Label mismatch between Service and Pods
- Readiness probe failing
- Pods not registered as endpoints

### Debug:
```bash
kubectl get endpoints <service-name>
kubectl describe svc <service-name>
kubectl describe pod <pod-name>
```

### Fix:
- Match labels and selectors
- Fix readiness probe
- Ensure pods are ready

---

## 🧠 3. Pod stuck in Pending… why?

The scheduler cannot place the pod on any node.

### Common reasons:
- Insufficient CPU/memory
- NodeSelector or affinity mismatch
- Taints without tolerations

### Debug:
```bash
kubectl describe pod <pod-name>
```

Look for:
- `Insufficient cpu`
- `node(s) didn't match node selector`

### Fix:
- Add more nodes or scale cluster
- Adjust resource requests
- Fix scheduling constraints

---

## 🧠 4. CrashLoopBackOff in production… how do you debug?

This indicates repeated container crashes.

### Step-by-step debugging:
```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

### Check:
- Exit codes
- OOMKilled events
- Liveness probe failures

### Common causes:
- Application error
- Memory limits exceeded
- Wrong config/env variables
- Bad startup command

### Fix:
- Increase memory
- Fix config
- Correct command/entrypoint

---

## 🧠 5. Pod running but readiness probe failing… impact?

### Impact:
- Pod is excluded from Service endpoints
- No traffic is routed

### Debug:
```bash
kubectl describe pod <pod-name>
```

### Fix:
- Correct health endpoint
- Increase `initialDelaySeconds`
- Ensure app is ready before probe

---

## 🧠 6. ImagePullBackOff… how to fix?

### Causes:
- Wrong image name/tag
- Private registry authentication issue

### Debug:
```bash
kubectl describe pod <pod-name>
```

### Fix:
- Correct image name
- Add imagePullSecrets
- Verify registry access

---

## 🧠 7. Pod works locally but fails in Kubernetes… why?

### Causes:
- Missing environment variables
- Config differences
- Network differences

### Debug:
- Compare local vs container env
- Check logs

### Fix:
- Use ConfigMap/Secret
- Validate configuration

---

## 🧠 8. Service not reachable from another pod… why?

### Causes:
- DNS resolution issue
- NetworkPolicy blocking
- Wrong service name

### Debug:
```bash
kubectl exec -it <pod> -- nslookup <service-name>
kubectl exec -it <pod> -- curl <service-name>
```

---

## 🧠 9. Ingress not working… why?

### Causes:
- Ingress controller not installed
- Wrong host/path
- Backend service issue

### Debug:
```bash
kubectl describe ingress
```

---

## 🧠 10. External traffic not reaching cluster… why?

### Causes:
- LoadBalancer not provisioned
- Security group blocking
- Firewall rules

---

## 🧠 11. Pod cannot access internet… why?

### Causes:
- No NAT gateway
- Egress blocked

---

## 🧠 12. PVC stuck in Pending… why?

### Causes:
- No StorageClass
- No available PV

### Debug:
```bash
kubectl get pvc
kubectl describe pvc <name>
```

---

## 🧠 13. Data lost after restart… why?

Using ephemeral storage (`emptyDir`).

### Fix:
Use PersistentVolume + PVC

---

## 🧠 14. Volume permission denied… why?

### Cause:
Incorrect security context

### Fix:
```yaml
securityContext:
  runAsUser: 1000
```

---

## 🧠 15. Pod cannot access cloud resources… why?

### Cause:
Missing IAM role / Workload Identity

---

## 🧠 16. Unauthorized API error… why?

### Cause:
RBAC misconfiguration

---

## 🧠 17. Traffic blocked between pods… why?

### Cause:
NetworkPolicy restricting traffic

---

## 🧠 18. Pod scheduled on wrong node… why?

### Cause:
Missing nodeSelector / affinity

---

## 🧠 19. Pod evicted… why?

### Cause:
Node resource pressure (memory/disk)

---

## 🧠 20. HPA not scaling… why?

### Causes:
- Metrics server missing
- No resource requests

### Debug:
```bash
kubectl get hpa
```

---

## 🧠 21. High CPU but no scaling… why?

HPA requires CPU requests defined.

---

## 🧠 22. Pods scaling but app still slow… why?

### Cause:
Bottleneck outside Kubernetes (DB/API)

---

## 🧠 23. Rolling update causing downtime… why?

### Causes:
- No readiness probe
- Low replicas

---

## 🧠 24. New version deployed but old still serving… why?

### Causes:
- Cache
- Ingress routing

---

## 🧠 25. Deployment stuck… why?

### Causes:
- Readiness probe failure
- Resource issues

---

## 🧠 26. Logs not visible… why?

### Causes:
- Wrong container
- Logging not configured

---

## 🧠 27. Node NotReady… why?

### Causes:
- Kubelet stopped
- Network issue

---

## 🧠 28. etcd full… impact?

Cluster becomes unstable and API server fails.

---

## 🧠 29. API server slow… why?

### Causes:
- High load
- etcd latency

---

## 🧠 30. How to design production-ready Kubernetes?

### Key components:
- Multi-node cluster
- Autoscaling (HPA + Cluster Autoscaler)
- Monitoring (Prometheus + Grafana)
- Secure networking (RBAC, NetworkPolicy)
- CI/CD integration

---

## 📌 Conclusion

If you can explain these scenarios with debugging steps,  
you are operating at a **real production-level Kubernetes engineer** 🚀

---

## ⭐ Support

- Star ⭐ the repo  
- Share with others  
- Follow for more DevOps content  
