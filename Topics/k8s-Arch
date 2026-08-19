# Kubernetes Architecture — Complete Guide
Kubernetes can look complicated because it has many components.
The easiest way to understand Kubernetes architecture is to divide the cluster into two major parts:
1. **Control Plane** — manages the cluster and makes decisions
2. **Worker Nodes** — run the applications
The simplest mental model is:
> **Control Plane decides and manages. Worker Nodes execute and run applications.**
---
# 1. High-Level Kubernetes Architecture
```text
                         KUBERNETES CLUSTER
                                |
               +----------------+----------------+
               |                                 |
               v                                 v
       +---------------+                 +---------------+
       | CONTROL PLANE |                 | WORKER NODES  |
       |   "MANAGES"   |                 |     "RUN"     |
       +---------------+                 +---------------+
               |                                 |
       +-------+-------+                 +--------+--------+
       |       |       |                 |        |        |
       v       v       v                 v        v        v
    API     etcd   Scheduler          Kubelet  Runtime  kube-proxy
   Server
               |
        Controllers
```
---
# 2. Control Plane
The **Control Plane is the brain of the Kubernetes cluster**.
It is responsible for:
- Managing the cluster
- Accepting API requests
- Storing cluster state
- Deciding where workloads should run
- Maintaining the desired state
- Communicating with cloud infrastructure when required
The major Control Plane components are:
1. API Server
2. etcd
3. Scheduler
4. Controller Manager
5. Cloud Controller Manager
Think of it like a manager in a company:
```text
Control Plane
      |
      +----> Receive requests
      |
      +----> Remember cluster state
      |
      +----> Decide where workloads should run
      |
      +----> Make sure desired state is maintained
      |
      +----> Communicate with cloud infrastructure
```
---
# 3. API Server
## What is the API Server?
The **Kubernetes API Server** is the central communication point for the Kubernetes cluster.
Users, administrators, `kubectl`, controllers, and other Kubernetes components communicate with the cluster through the API Server.
```text
kubectl
   |
   v
API Server
   |
   +----> etcd
   |
   +----> Scheduler
   |
   +----> Controllers
   |
   +----> Other Kubernetes Components
```
## Why do we need the API Server?
Kubernetes has many different components.
We need a central and controlled way for these components to communicate.
Instead of every component directly communicating with every other component, the API Server provides a common interface.
For example, when you run:
```bash
kubectl apply -f deployment.yaml
```
the request goes to the API Server.
The API Server:
1. Receives the request
2. Authenticates the requester
3. Checks authorization
4. Validates the request
5. Processes the Kubernetes API request
6. Persists the resulting state through `etcd`
So the API Server acts as the **central gateway to the Kubernetes cluster**.
## Simple way to remember
> **API Server = Communication gateway**
---
# 4. etcd
## What is etcd?
`etcd` is Kubernetes' **distributed key-value data store**.
It stores the persistent state of the Kubernetes cluster.
It contains information about things such as:
- Cluster configuration
- Nodes
- Workloads
- Services
- Secrets
- Desired state
- Kubernetes resource metadata
## Why do we need etcd?
Kubernetes needs a reliable place to remember the state of the cluster.
For example, suppose you tell Kubernetes:
```text
I want 3 replicas of my application.
```
Kubernetes needs to remember that desired state.
```text
User
  |
  v
API Server
  |
  v
etcd
Desired State:
3 replicas
```
Controllers can then use the stored state while continuously reconciling the environment.
## Example
Suppose the desired state is:
```text
Desired = 3 Pods
```
The cluster needs to remember that requirement.
If one Pod disappears:
```text
Desired = 3
Actual  = 2
```
Kubernetes can determine that something needs to be fixed.
## Important
`etcd` is effectively the **source of truth for Kubernetes cluster state**.
Because it contains critical cluster information, it should be:
- Properly secured
- Access-controlled
- Encrypted in transit
- Regularly backed up
## Simple way to remember
> **etcd = Kubernetes' memory**
---
# 5. Scheduler
## What is the Scheduler?
The **Kubernetes Scheduler decides which Worker Node should run a newly created Pod**.
Imagine a cluster with three Worker Nodes:
```text
Worker Node 1
Worker Node 2
Worker Node 3
```
Kubernetes needs to answer:
> **"Which node should run this workload?"**
The Scheduler makes that decision.
```text
New Workload
     |
     v
Scheduler
     |
     +----> Worker Node 1
     |
     +----> Worker Node 2
     |
     +----> Worker Node 3
```
## Why do we need the Scheduler?
A Kubernetes cluster can have many Worker Nodes.
We need a component that evaluates those nodes and selects a suitable one for the workload.
The Scheduler considers things such as:
- Available CPU
- Available memory
- Resource requests
- Node selectors
- Node affinity
- Pod affinity
- Pod anti-affinity
- Taints and tolerations
- Topology constraints
For example:
```text
Application requires:
CPU    = 2 cores
Memory = 4 GB
```
The Scheduler looks at the available nodes and chooses one that satisfies the scheduling requirements.
## Simple way to remember
> **Scheduler = Decides where workloads run**
---
# 6. Controller Manager
## What is the Controller Manager?
The **Controller Manager runs Kubernetes controllers**.
Controllers continuously compare:
```text
Desired State
     vs
Actual State
```
If the two are different, controllers take action to bring the actual state closer to the desired state.
## Why do we need controllers?
Kubernetes is based heavily on the concept of **desired state**.
For example, suppose we specify:
```yaml
replicas: 3
```
We are telling Kubernetes:
> "I want three instances of this workload running."
Initially:
```text
Desired = 3
Actual  = 3
```
Everything is fine.
Now suppose one Pod disappears:
```text
Desired = 3
Actual  = 2
```
A controller notices this difference and takes action.
```text
Desired = 3
Actual  = 2
     |
     v
Controller
     |
     v
Create replacement
     |
     v
Actual = 3
```
## Why is this important?
This is one of the most important concepts in Kubernetes:
> **Kubernetes continuously works to make the actual state match the desired state.**
The Controller Manager manages different controllers responsible for different types of resources.
## Simple way to remember
> **Controller Manager = Keeps actual state aligned with desired state**
---
# 7. Cloud Controller Manager
## What is the Cloud Controller Manager?
The **Cloud Controller Manager (CCM)** allows Kubernetes to interact with cloud-provider-specific functionality.
It is commonly used when Kubernetes runs on cloud platforms such as:
- AWS
- Azure
- Google Cloud
## Why do we need it?
Kubernetes is designed to be cloud-agnostic.
However, cloud providers have their own APIs and infrastructure.
For example, Kubernetes may need to interact with a cloud provider to:
- Create or manage a cloud load balancer
- Understand cloud-specific nodes
- Configure cloud routes
- Integrate with other cloud infrastructure
The Cloud Controller Manager provides this integration.
```text
Kubernetes
     |
     v
Cloud Controller Manager
     |
     v
Cloud Provider APIs
     |
     +----> AWS
     |
     +----> Azure
     |
     +----> Google Cloud
```
## Simple way to remember
> **Cloud Controller Manager = Kubernetes ↔ Cloud integration**
---
# 8. Worker Nodes
## What are Worker Nodes?
Worker Nodes are the machines where **application workloads actually run**.
A Kubernetes cluster can have multiple Worker Nodes.
```text
                 CONTROL PLANE
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
      Worker 1    Worker 2    Worker 3
          |           |           |
      Workloads   Workloads   Workloads
```
The Control Plane makes decisions, while Worker Nodes execute those decisions.
Each Worker Node normally contains:
- Kubelet
- Container Runtime
- kube-proxy
---
# 9. Kubelet
## What is Kubelet?
**Kubelet is the Kubernetes agent running on every Worker Node.**
It communicates with the Kubernetes API Server and makes sure the workloads assigned to its node are running correctly.
## Why do we need Kubelet?
Suppose the Scheduler decides:
```text
Run this workload on Worker Node 2.
```
Someone on Worker Node 2 needs to make that happen.
That's the Kubelet's responsibility.
```text
Control Plane
      |
      v
API Server
      |
      v
Kubelet
      |
      v
Container Runtime
      |
      v
Container
```
Kubelet:
- Receives workload specifications
- Communicates with the container runtime
- Makes sure containers are running
- Monitors workloads
- Reports status back to the API Server
## Simple way to remember
> **Kubelet = Worker Node supervisor**
---
# 10. Container Runtime
## What is a Container Runtime?
The **Container Runtime is responsible for actually running containers**.
Common container runtimes include:
- `containerd`
- `CRI-O`
## Why do we need a Container Runtime?
Kubelet manages the workload, but it does not directly implement the low-level container execution.
Kubelet communicates with the Container Runtime.
```text
Kubelet
   |
   v
Container Runtime
   |
   +----> Pull Image
   |
   +----> Create Container
   |
   +----> Start Container
   |
   +----> Stop Container
```
For example:
```text
Kubelet
   |
   | "Run nginx image"
   v
Container Runtime
   |
   v
Pull nginx image
   |
   v
Create container
   |
   v
Start container
```
## Simple way to remember
> **Container Runtime = Actually runs containers**
---
# 11. kube-proxy
## What is kube-proxy?
`kube-proxy` is a networking component that runs on Worker Nodes.
It maintains network rules that help Kubernetes Services direct traffic toward the appropriate Pods.
## Why do we need kube-proxy?
Pods are dynamic.
They can:
- Start
- Stop
- Crash
- Be replaced
- Move between nodes
Because of this, Pod IP addresses can change.
Kubernetes Services provide a stable way to access a group of Pods.
`kube-proxy` helps implement the networking behavior required for Service traffic to reach the appropriate Pods.
```text
Client
   |
   v
Service
   |
   v
Network Rules
   |
   v
Pod
```
## Simple way to remember
> **kube-proxy = Helps Service traffic reach the right Pods**
## Important Note
Modern Kubernetes networking can use different implementations, including IPVS and eBPF-based networking.
In some environments, networking functionality traditionally associated with `kube-proxy` can be provided by the networking implementation itself.
---
# 12. Complete Control Plane Architecture
```text
                         CONTROL PLANE
                            "MANAGES"
                                |
                                v
                     +--------------------+
                     |     API SERVER     |
                     |                    |
                     | Central Gateway    |
                     +---------+----------+
                               |
              +----------------+----------------+
              |                |                |
              v                v                v
         +---------+      +---------+      +-------------+
         |  etcd   |      |Scheduler|      | Controllers |
         |         |      |         |      |             |
         | Cluster |      | Decides |      | Reconciles  |
         |  State  |      | Where   |      | State       |
         +---------+      +---------+      +-------------+
                               |
                               v
                       Worker Node Selection
```
---
# 13. Complete Worker Node Architecture
```text
                         WORKER NODE
                              |
              +---------------+---------------+
              |               |               |
              v               v               v
          Kubelet      Container Runtime   kube-proxy
              |               |               |
              |               v               |
              |          Containers           |
              |                               |
              +---------------+---------------+
                              |
                              v
                    Application Workloads
```
---
# 14. Complete Kubernetes Architecture
```text
                         KUBERNETES CLUSTER
                                |
               +----------------+----------------+
               |                                 |
               v                                 v
       +-------------------+             +-------------------+
       |   CONTROL PLANE   |             |   WORKER NODES    |
       |     "MANAGES"     |             |       "RUN"       |
       +-------------------+             +-------------------+
       |                   |             |                   |
       | API Server        |             | Kubelet           |
       |                   |             |                   |
       | etcd              |             | Container Runtime |
       |                   |             |                   |
       | Scheduler         |             | kube-proxy        |
       |                   |             |                   |
       | Controller        |             | Application       |
       | Manager           |             | Workloads         |
       |                   |             |                   |
       | Cloud Controller  |             |                   |
       | Manager           |             |                   |
       +---------+---------+             +-------------------+
                 |
                 +-----------------------+
                 |                       |
                 v                       v
           Worker Node 1           Worker Node 2
                 |
                 v
           Worker Node 3
```
---
# 15. How Kubernetes Components Work Together
Let's understand the complete flow using a simple example.
Suppose you want Kubernetes to run a new application workload.
---
## Step 1 — User Sends a Request
You run:
```bash
kubectl apply -f application.yaml
```
The request goes to the API Server.
```text
kubectl
   |
   v
API Server
```
The API Server is the central entry point for the request.
---
## Step 2 — API Server Processes the Request
The API Server:
1. Authenticates the request
2. Checks authorization
3. Validates the request
4. Processes the API operation
5. Persists the cluster state
The state is stored in `etcd`.
```text
kubectl
   |
   v
API Server
   |
   v
etcd
```
---
## Step 3 — Scheduler Chooses a Worker Node
If the workload needs a new Pod and does not yet have a Worker Node assigned, the Scheduler evaluates the available nodes.
```text
API Server
     |
     v
Scheduler
     |
     +----> Worker Node 1
     |
     +----> Worker Node 2
     |
     +----> Worker Node 3
```
Suppose Worker Node 2 is selected.
```text
Scheduler
     |
     v
Worker Node 2
```
---
## Step 4 — Kubelet Takes Over
The Kubelet running on Worker Node 2 sees that the workload needs to run on its node.
```text
API Server
     |
     v
Kubelet on Worker Node 2
```
The Kubelet works with the Container Runtime.
---
## Step 5 — Container Runtime Runs the Container
The Kubelet instructs the Container Runtime to run the required container.
```text
Kubelet
   |
   v
Container Runtime
   |
   +----> Pull Image
   |
   +----> Create Container
   |
   +----> Start Container
```
The application is now running on the Worker Node.
---
## Step 6 — Networking
When application traffic needs to reach the workload through a Kubernetes Service, Kubernetes networking rules direct the traffic toward the appropriate Pod.
A simplified view:
```text
Client
   |
   v
Service
   |
   v
Networking
   |
   v
Pod
```
`kube-proxy` traditionally helps implement the Service networking rules on Worker Nodes.
---
## Step 7 — Kubernetes Continuously Reconciles the State
Kubernetes does not simply deploy the workload and forget about it.
Controllers continuously compare:
```text
Desired State
     |
     | Compare
     v
Actual State
```
For example:
```text
Desired = 3 Pods
Actual  = 2 Pods
      |
      v
Controller
      |
      v
Create replacement
      |
      v
Actual = 3 Pods
```
This continuous reconciliation is one of the core principles of Kubernetes.
---
# 16. Complete Request-to-Application Flow
The simplified flow looks like this:
```text
Developer
    |
    | kubectl apply
    v
API Server
    |
    +--------------------+
    |                    |
    v                    v
  etcd              Scheduler
                         |
                         | Select Node
                         v
                    Worker Node
                         |
                         v
                      Kubelet
                         |
                         v
                 Container Runtime
                         |
                         v
                    Container
                         |
                         v
                   Application
```
---
# 17. Desired State vs Actual State
This is one of the most important Kubernetes concepts.
## Desired State
You tell Kubernetes what you want.
For example:
```text
I want 3 replicas.
```
## Actual State
Kubernetes checks what currently exists.
```text
Currently running:
2 replicas
```
Kubernetes sees:
```text
Desired = 3
Actual  = 2
```
Controllers take action to correct the difference.
```text
Desired = 3
Actual  = 2
     |
     v
Controller
     |
     v
Create replacement
     |
     v
Desired = 3
Actual  = 3
```
This process continuously happens in the background.
---
# 18. What Happens If a Container Crashes?
Suppose an application container crashes.
```text
Application
     |
     X
Container crashes
```
The Kubelet detects the container state and works with the Container Runtime to restart it according to the workload's restart behavior.
If the Pod itself needs to be replaced and the desired number of replicas is no longer satisfied, the appropriate Kubernetes controller can create a replacement.
Simplified:
```text
Container Crash
      |
      v
Kubelet detects
      |
      v
Container Runtime
      |
      v
Restart / Recover
```
If the desired number of Pods is no longer satisfied:
```text
Desired = 3
Actual  = 2
      |
      v
Controller
      |
      v
Replacement workload
```
This is part of Kubernetes' self-healing behavior.
---
# 19. Control Plane vs Worker Node
| Control Plane | Worker Node |
|---|---|
| Manages the cluster | Runs application workloads |
| Makes scheduling decisions | Executes assigned workloads |
| Stores cluster state through etcd | Runs containers |
| Runs controllers | Runs Kubelet |
| Handles Kubernetes API requests | Runs Container Runtime |
| Maintains desired state | Provides node-level networking |
| Communicates with cloud providers when configured | Hosts application workloads |
---
# 20. Component Summary
| Component | What It Does | Why We Need It |
|---|---|---|
| **API Server** | Handles Kubernetes API requests | Provides a central communication point |
| **etcd** | Stores cluster state | Kubernetes needs persistent memory |
| **Scheduler** | Selects a Worker Node for new Pods | Determines where workloads should run |
| **Controller Manager** | Reconciles desired and actual state | Keeps the cluster in the desired state |
| **Cloud Controller Manager** | Handles cloud-specific integration | Allows Kubernetes to interact with cloud infrastructure |
| **Kubelet** | Manages workloads on a Worker Node | Makes sure assigned workloads are running |
| **Container Runtime** | Runs containers | Actually creates and runs containers |
| **kube-proxy** | Maintains Service networking rules | Helps traffic reach the correct Pods |
---
# 21. Easy Memory Trick
Remember the Control Plane like this:
```text
API Server
    ↓
COMMUNICATE
etcd
    ↓
REMEMBER
Scheduler
    ↓
DECIDE WHERE
Controller Manager
    ↓
RECONCILE
Cloud Controller Manager
    ↓
CONNECT TO CLOUD
```
Remember the Worker Node like this:
```text
Kubelet
    ↓
MANAGE
Container Runtime
    ↓
RUN
kube-proxy
    ↓
NETWORK
```
---
# 22. One-Minute Interview Answer
If an interviewer asks:
> **"Can you explain Kubernetes architecture?"**
A concise senior-level answer would be:
> "I divide Kubernetes architecture into two parts: the Control Plane and Worker Nodes.
>
> The Control Plane manages the cluster. The API Server is the central communication point, etcd stores the cluster state, the Scheduler decides where new workloads should run, and the Controller Manager continuously reconciles the actual state with the desired state. In cloud environments, the Cloud Controller Manager handles cloud-specific integrations.
>
> Worker Nodes are where the applications actually run. Kubelet is the node-level agent that makes sure assigned workloads are running, the Container Runtime actually runs the containers, and kube-proxy maintains the networking rules needed for Kubernetes Services.
>
> So the simple mental model is: **Control Plane decides and manages, Worker Nodes execute and run the applications.**"
---
# 23. Final Mental Model
```text
                    KUBERNETES
                         |
          +--------------+--------------+
          |                             |
          v                             v
   CONTROL PLANE                  WORKER NODES
      "DECIDES"                      "RUNS"
          |                             |
    +-----+------+                +-----+------+
    |     |      |                |     |      |
    v     v      v                v     v      v
   API   etcd Scheduler         Kubelet Runtime kube-proxy
 Server
    |
    +---- Controllers
    |
    +---- Cloud Controller
          Manager
```
## The simplest way to remember Kubernetes
> **API Server → Communicate**
> **etcd → Remember**
> **Scheduler → Decide where**
> **Controllers → Reconcile**
> **Cloud Controller Manager → Connect to cloud**
> **Kubelet → Manage workloads on the node**
> **Container Runtime → Run containers**
> **kube-proxy → Help Service traffic reach workloads**
---
# Final Takeaway
> **Control Plane = DECIDE & MANAGE**
> **Worker Nodes = EXECUTE & RUN**
The complete Kubernetes flow can therefore be remembered as:
```text
User
  |
  v
API Server
  |
  +----> etcd stores cluster state
  |
  +----> Scheduler decides where workload runs
  |
  +----> Controllers maintain desired state
  |
  v
Worker Node
  |
  +----> Kubelet manages the workload
  |
  +----> Container Runtime runs containers
  |
  +----> kube-proxy helps Service networking
  |
  v
Application
```
