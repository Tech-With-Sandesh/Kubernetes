# 🚀 Kubernetes Debugging Guide: CrashLoopBackOff in Production

## 📌 Overview

CrashLoopBackOff is one of the most common and critical issues in Kubernetes.

> Pod keeps restarting repeatedly and never becomes stable.

This guide explains:
- What CrashLoopBackOff means
- Step-by-step debugging approach
- Real production root causes
- Fix strategies

---

## 🧠 What is CrashLoopBackOff?

CrashLoopBackOff means:
- Container starts → crashes → Kubernetes restarts it
- This loop continues with increasing delay

---

## 🎯 Goal

Identify:
- Why the container is crashing
- Fix the root cause
- Prevent repeated failures

---

## 🔍 Step 1: Check Pod Status

```bash
kubectl get pods
```

Look for:
- STATUS: CrashLoopBackOff
- Restart count increasing

---

## 🔍 Step 2: Describe the Pod

```bash
kubectl describe pod <pod-name>
```

Check:
- Events section
- Errors (OOMKilled, Back-off restarting)

---

## 🔍 Step 3: Check Container Logs

```bash
kubectl logs <pod-name>
```

If restarting fast:

```bash
kubectl logs <pod-name> --previous
```

👉 This shows logs from the last failed container

---

## ⚠️ Step 4: Check Exit Code

From `describe pod`:

| Exit Code | Meaning |
|----------|--------|
| 1 | Application error |
| 137 | OOMKilled (Out of Memory) |
| 126/127 | Command not found |
| 139 | Segmentation fault |

---

## ⚡ Step 5: Check Resource Limits (OOMKilled)

### Problem:
Container exceeds memory limit → killed

### Check:

```bash
kubectl describe pod <pod-name>
```

Look for:
- `OOMKilled`

### Fix:

```yaml
resources:
  requests:
    memory: "512Mi"
  limits:
    memory: "1Gi"
```

---

## 🔍 Step 6: Check Application Configuration

### Common issues:
- Wrong environment variables
- Missing configs
- Invalid startup command

### Fix:
- Verify env variables
- Validate config files
- Test container locally

---

## 🔍 Step 7: Check Liveness Probe

### Problem:
Liveness probe fails → Kubernetes restarts container

### Example issue:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

If endpoint fails → restart loop

### Fix:
- Correct endpoint
- Increase initial delay

```yaml
initialDelaySeconds: 30
```

---

## 🔍 Step 8: Check Readiness Probe

### Problem:
Readiness probe failing → traffic not routed

(Not direct crash cause but impacts behavior)

---

## 🔍 Step 9: Check Image Issues

### Problems:
- Wrong image tag
- Corrupt image
- Missing dependencies

### Fix:
- Verify image exists
- Pull locally and test

---

## 🔍 Step 10: Check Command / Entrypoint

### Problem:
Wrong command causes immediate exit

### Example:

```yaml
command: ["wrong-command"]
```

### Fix:
- Validate entrypoint
- Test container manually

---

## 🔍 Step 11: Dependency Issues

### Problem:
App depends on:
- Database
- External API

If dependency is down → app crashes

### Fix:
- Add retry logic
- Use initContainers
- Wait for dependencies

---

## 🔍 Step 12: Check Volume Mounts

### Problem:
- Missing volume
- Permission issues

### Fix:
- Verify mounts
- Check file permissions

---

## 🔍 Step 13: Node Issues

### Problem:
- Node resource pressure
- Disk/memory issues

### Check:

```bash
kubectl describe node
```

---

## 📊 Common Root Causes

| Issue | Impact |
|------|--------|
| OOMKilled | Memory exceeded |
| Bad config | App crash |
| Liveness probe | Restart loop |
| Wrong command | Immediate exit |
| Dependency failure | Crash |
| Image issue | Startup failure |

---

## 🎯 Final Debugging Checklist

- [ ] Checked pod logs (`--previous`)
- [ ] Verified exit code
- [ ] Checked resource limits
- [ ] Validated env/config
- [ ] Verified probes
- [ ] Checked image and command
- [ ] Verified dependencies

---

## 🚀 Best Practices

- Always set resource requests/limits
- Use proper liveness/readiness probes
- Add retry logic in apps
- Validate configs before deploy
- Monitor pod restarts

---

## 📌 Conclusion

CrashLoopBackOff is not random.

It is usually caused by:
- Resource limits
- Misconfiguration
- Probe failures
- Dependency issues

Systematic debugging helps resolve it quickly 🚀

---

## ⭐ If this helped

- Star ⭐ the repo  
- Share with others  
- Follow for more DevOps content  

---
