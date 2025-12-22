# Linux Namespaces — The 7 Most Used (Beginner-Friendly Guide)

Namespaces are a **Linux kernel feature** that isolate resources for processes.  
This is the foundational technology behind **containers (Docker)** and **Kubernetes Pods**.

When a container runs, Kubernetes creates a **new set of namespaces**, making the container think it has its own system.

---

## The 7 Most Common Linux Namespaces

| Namespace | Symbol | Isolates | Example |
|-----------|--------|----------|---------|
| PID       | `pid`  | Process IDs | Each container has its own PID 1 |
| Network   | `net`  | Network stack | Each container gets its own NIC, IP |
| Mount     | `mnt`  | Filesystem mount points | Containers can't see host filesystem |
| UTS       | `uts`  | Hostname & domain name | Each container can have its own hostname |
| IPC       | `ipc`  | Inter-process communication | Isolated shared memory & semaphores |
| User      | `user` | User & group IDs | Map container user to host user |
| Cgroup    | `cgroup` | Resource limits | CPU/memory limits enforced |

---

## 1. PID Namespace

###  What It Is
Isolates **process IDs**.  
Each container gets its own **PID 1**, and processes inside can’t see host processes.

### Benefits
- Better security — cannot inspect host processes  
- Cleaner process tree  
- Allows container-specific init systems  

### How Kubernetes Uses It
- Each Pod container runs inside a PID namespace  
- Optional: Pods can share PID namespace (`shareProcessNamespace: true`)  

---

## 2. Network Namespace

### What It Is
Provides each container its own:
- Network interface
- IP address
- Routing table
- Firewall rules

###  Benefits
- Full network isolation  
- Containers don’t conflict on ports  
- Container network can be controlled independently  

### How Kubernetes Uses It
- Every Pod gets **one network namespace** shared by all containers in that Pod  
- CNI plugins (Calico, Flannel, Cilium) create virtual interfaces inside namespaces  

---

## 3. Mount (mnt) Namespace

### What It Is
Isolates filesystem mounts:
- Root filesystem  
- `/proc`
- `/dev`
- Volumes

### Benefits
- Container only sees its own filesystem  
- Prevents containers from modifying host files  
- Enables overlay filesystems (unionfs, overlayfs)

### How Kubernetes Uses It
- Container runtime mounts:
  - Image filesystem  
  - Volumes (PVC, emptyDir, configMap, secret)  
- Ensures each Pod has isolated view of storage  

---

## 4. UTS Namespace

UTS = UNIX Time-sharing System

### What It Is
Isolates:
- Hostname  
- Domain name  

### Benefits
- Container can have its own hostname  
- Useful for distributed systems identifying nodes  

### How Kubernetes Uses It
- Each Pod gets a unique hostname based on Pod name  
- Helps with DNS in Kubernetes  

---

## 5. IPC Namespace

### What It Is
Isolates mechanisms used for **inter-process communication**, like:
- Shared memory (`/dev/shm`)
- Semaphores
- Message queues

### Benefits
- Containers cannot access host shared memory  
- Prevents one container interfering with another  

### How Kubernetes Uses It
- By default: Pod containers share IPC namespace  
- Can isolate it using Pod settings  

---

## 6. User Namespace

### What It Is
Isolates:
- User IDs  
- Group IDs  

This allows:
- Container user `root` ≠ Host `root`  
- UID/GID mapping  

###  Benefits
- Big security improvement  
- Run root in containers without giving real root privileges  

### How Kubernetes Uses It
- User namespaces are becoming more supported in Kubernetes  
- Used in hardened security setups  
- Often used in Pod Security Standards (PSS)

---

## 7. Cgroup Namespace

### 💡 What It Is
Isolates view of **cgroups**, which control:
- CPU limits  
- Memory limits  
- Disk I/O  
- Process count  

### Benefits
- Container sees only its resource limits  
- Keeps resource usage independent  

### How Kubernetes Uses It
- Kubernetes relies heavily on cgroups to enforce limits:
```yaml
resources:
  limits:
    cpu: "1"
    memory: "512Mi"
