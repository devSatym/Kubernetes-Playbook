> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🐳 crictl — Container Runtime Debugging

> **What it does:** CLI for CRI-compatible container runtimes (containerd, CRI-O). Like `docker` but for Kubernetes.  
> **Why CKA cares:** When pods are stuck, containers won't start, or kubelet can't talk to the runtime — crictl is how you debug.

---

## 🧠 Core Concept

```
kubectl (API level)
    │
    ▼
kubelet (node agent)
    │
    ▼
CRI (Container Runtime Interface)
    │
    ▼
containerd / CRI-O (actual runtime)
    │
    ▼
crictl (CLI to talk directly to CRI)
```

**When to use crictl vs kubectl:**
- `kubectl` = cluster-level (goes through API server)
- `crictl` = node-level (talks directly to container runtime)
- Use `crictl` when kubelet or API server is DOWN

### Config (usually pre-configured)
```bash
# crictl config
cat /etc/crictl.yaml
# runtime-endpoint: unix:///run/containerd/containerd.sock
# image-endpoint: unix:///run/containerd/containerd.sock
```

---

## 🔥 Essential crictl Commands

### Container Operations

| Command | What It Does |
|---------|-------------|
| `crictl ps` | List running containers |
| `crictl ps -a` | List ALL containers (including stopped) |
| `crictl ps -a --name <name>` | Filter by name |
| `crictl inspect <container-id>` | Full container details (JSON) |
| `crictl logs <container-id>` | Container logs |
| `crictl logs -f <container-id>` | Follow logs |
| `crictl logs --tail 50 <container-id>` | Last 50 lines |
| `crictl exec -it <container-id> sh` | Exec into container |
| `crictl stop <container-id>` | Stop a container |
| `crictl rm <container-id>` | Remove a container |

### Pod Operations

| Command | What It Does |
|---------|-------------|
| `crictl pods` | List pods |
| `crictl pods --name <name>` | Filter pods by name |
| `crictl pods --state Ready` | Filter by state |
| `crictl pods --state NotReady` | Find broken pods |
| `crictl inspectp <pod-id>` | Inspect pod sandbox |
| `crictl stopp <pod-id>` | Stop pod |
| `crictl rmp <pod-id>` | Remove pod |

### Image Operations

| Command | What It Does |
|---------|-------------|
| `crictl images` | List images |
| `crictl pull <image>` | Pull image |
| `crictl rmi <image>` | Remove image |
| `crictl imagefsinfo` | Image filesystem info |

---

## ⚡ CKA Speed Combos

### 🔴 Pod Stuck in ContainerCreating
```bash
# 1. Check if containers are created at runtime level
crictl ps -a | grep <pod-name-fragment>

# 2. If container exists but failed, check logs
crictl logs <container-id>

# 3. Check pod sandbox
crictl pods --name <pod-name>
crictl inspectp <pod-id> | grep -i "error\|state"
```

### 🔴 Static Pod Not Starting
```bash
# Static pods are managed by kubelet → containers visible via crictl
crictl ps -a | grep "kube-apiserver\|kube-controller\|kube-scheduler\|etcd"

# Check specific static pod container logs
crictl ps -a --name kube-apiserver
crictl logs <container-id>
```

### 🔴 Container Runtime Not Working
```bash
# Check if containerd is running
systemctl status containerd

# Check if crictl can connect
crictl info

# List containers (if this fails, runtime is broken)
crictl ps
```

### 🔴 Find Crashed Containers
```bash
# List all non-running containers
crictl ps -a | grep -v "Running"

# Get logs from crashed container
crictl ps -a --name <pod-name>
crictl logs <container-id>
```

### 🔴 Clean Up (Disk Space Issues)
```bash
# Remove stopped containers
crictl rm $(crictl ps -a -q --state Exited)

# Remove unused images
crictl rmi --prune
```

---

## 🎯 CKA Exam Patterns

| Scenario | Command |
|----------|---------|
| List all containers | `crictl ps -a` |
| Find crashed containers | `crictl ps -a \| grep -v Running` |
| Get container logs | `crictl logs <container-id>` |
| Check static pod containers | `crictl ps -a --name kube-apiserver` |
| Verify runtime is working | `crictl info` |
| Check pod sandboxes | `crictl pods` |
| Exec into container | `crictl exec -it <id> sh` |

---

## 💡 Pro Tips

1. **Use `crictl` when kubectl doesn't work** — if API server or kubelet is down, crictl still works
2. **`crictl ps -a`** shows ALL containers including exited — crucial for seeing crash loops
3. **Static pod debugging** — static pods (apiserver, scheduler, etc.) are best debugged via `crictl logs`
4. **`crictl info`** — quick check if container runtime is responding
5. **Short IDs work** — you don't need the full container ID, first few characters are enough
6. **`crictl` ≠ `docker`** — similar commands but slightly different syntax (no `docker ps` anymore)

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->