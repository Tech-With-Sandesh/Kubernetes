## 📦 Basic Commands

```bash
kubectl cluster-info        # Shows cluster information
kubectl version             # Displays client and server version
kubectl get nodes           # Lists all nodes in the cluster
kubectl get pods            # Lists all pods in current namespace
kubectl get all             # Lists all resources in current namespace
kubectl config view         # Shows kubeconfig details
kubectl config use-context <context>  # Switches cluster context
```

---

## 📁 Namespace Commands

```bash
kubectl get ns              # Lists all namespaces
kubectl create ns dev       # Creates a new namespace
kubectl delete ns dev       # Deletes a namespace
kubectl config set-context --current --namespace=dev  # Sets default namespace
```

---

## 🐳 Pod Commands

```bash
kubectl run nginx --image=nginx         # Creates a pod using nginx image
kubectl get pods -o wide                # Shows pods with node details
kubectl describe pod <pod-name>         # Shows detailed pod info
kubectl logs <pod-name>                 # Displays pod logs
kubectl exec -it <pod-name> -- /bin/sh  # Access pod shell
kubectl delete pod <pod-name>           # Deletes a pod
kubectl port-forward pod/<pod-name> 8080:80  # Forwards local port to pod
```

---

## 🚀 Deployment Commands

```bash
kubectl create deployment nginx --image=nginx  # Creates a deployment
kubectl get deployments                       # Lists deployments
kubectl describe deployment nginx             # Shows deployment details
kubectl scale deployment nginx --replicas=3   # Scales deployment
kubectl rollout status deployment nginx       # Checks rollout status
kubectl rollout history deployment nginx      # Shows rollout history
kubectl rollout undo deployment nginx         # Rolls back deployment
kubectl delete deployment nginx               # Deletes deployment
```

---

## 🌐 Service Commands

```bash
kubectl get svc                              # Lists services
kubectl expose deployment nginx --type=NodePort --port=80  # Exposes deployment
kubectl describe svc nginx                   # Shows service details
kubectl delete svc nginx                     # Deletes service
```

---

## 📄 ConfigMap & Secret Commands

```bash
kubectl create configmap app-config --from-literal=key=value  # Creates configmap
kubectl get configmap                     # Lists configmaps
kubectl describe configmap app-config     # Shows configmap details
kubectl create secret generic my-secret --from-literal=password=1234  # Creates secret
kubectl get secrets                       # Lists secrets
kubectl describe secret my-secret         # Shows secret details
```

---

## 📦 Resource Management (YAML)

```bash
kubectl apply -f app.yaml        # Creates/updates resources from YAML
kubectl create -f app.yaml       # Creates resources from YAML
kubectl delete -f app.yaml       # Deletes resources from YAML
kubectl replace -f app.yaml      # Replaces resource
kubectl edit deployment nginx    # Edits resource live
```

---

## 🔍 Debugging & Monitoring

```bash
kubectl top pod                  # Shows pod resource usage
kubectl top node                 # Shows node resource usage
kubectl get events               # Lists cluster events
kubectl describe node <node>     # Shows node details
kubectl logs -f <pod-name>       # Streams pod logs
```

---

## 🔐 Advanced Commands

```bash
kubectl label pod <pod> env=prod        # Adds label to pod
kubectl get pods -l env=prod            # Filters pods by label
kubectl annotate pod <pod> note=test    # Adds annotation
kubectl cordon <node>                  # Marks node unschedulable
kubectl uncordon <node>                # Marks node schedulable
kubectl drain <node>                  # Evicts pods from node
```

