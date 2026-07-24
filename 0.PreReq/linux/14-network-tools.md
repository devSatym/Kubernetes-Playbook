> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🌐 ss, ip, nslookup, netstat — Network Troubleshooting

> **What they do:** Inspect network connections, interfaces, routes, and DNS from the node level.  
> **Why CKA cares:** Debugging services, NetworkPolicies, DNS issues, and node networking.

---

## 🔥 ss — Socket Statistics (Modern netstat)

### Essential Commands

| Command | What It Does |
|---------|-------------|
| `ss -tlnp` | **THE command** — TCP listening ports + process |
| `ss -ulnp` | UDP listening ports + process |
| `ss -tnp` | Active TCP connections + process |
| `ss -s` | Socket summary (counts) |

### Flag Breakdown

| Flag | Meaning |
|------|---------|
| `-t` | TCP sockets |
| `-u` | UDP sockets |
| `-l` | Listening only |
| `-n` | Numeric (don't resolve names — faster) |
| `-p` | Show process using the socket |
| `-a` | All sockets (listening + established) |
| `-4` | IPv4 only |

### CKA Combos

```bash
# Is API server listening on 6443?
ss -tlnp | grep 6443

# Is kubelet listening on 10250?
ss -tlnp | grep 10250

# Is etcd listening on 2379?
ss -tlnp | grep 2379

# What's using port 80?
ss -tlnp | grep :80

# Is kube-proxy listening?
ss -tlnp | grep kube-proxy

# All listening ports with processes
ss -tlnp
```

---

## 🔥 ip — Network Interface & Route Management

### Essential Commands

| Command | What It Does |
|---------|-------------|
| `ip addr` or `ip a` | Show all interfaces + IP addresses |
| `ip addr show eth0` | Show specific interface |
| `ip route` or `ip r` | Show routing table |
| `ip route get <IP>` | Which route is used to reach IP |
| `ip link` | Show link-layer info (up/down status) |
| `ip link show eth0` | Specific interface link info |
| `ip -br addr` | **Brief** — compact IP listing |
| `ip neigh` | ARP table (neighbor cache) |

### CKA Combos

```bash
# Get node's internal IP
ip -br addr | grep -v "lo\|docker\|veth\|cni"

# Get the default route (gateway)
ip route | grep default

# Check which interface reaches a pod/service IP
ip route get 10.244.1.5

# Check if an interface is UP
ip link show eth0 | grep "state UP"

# View all routes (including pod network routes)
ip route
```

---

## 🔥 nslookup / dig — DNS Debugging

### nslookup (available in busybox)

```bash
# Resolve service DNS
nslookup kubernetes.default.svc.cluster.local

# Resolve from inside a pod
kubectl run tmp --image=busybox --rm -it --restart=Never -- nslookup <svc-name>.<namespace>

# Resolve with specific DNS server
nslookup <hostname> <dns-server-ip>
```

### dig (more detailed)

```bash
# Full DNS lookup
dig kubernetes.default.svc.cluster.local

# Short answer only
dig +short kubernetes.default.svc.cluster.local

# Query specific DNS server
dig @10.96.0.10 <svc-name>.<namespace>.svc.cluster.local

# Reverse lookup
dig -x 10.244.0.5
```

### CKA DNS Debugging Flow

```bash
# 1. Check CoreDNS pods are running
kubectl get pods -n kube-system -l k8s-app=kube-dns

# 2. Check CoreDNS service IP
kubectl get svc -n kube-system kube-dns

# 3. Test DNS resolution from a pod
kubectl run dns-test --image=busybox --rm -it --restart=Never -- nslookup kubernetes.default

# 4. Test cross-namespace DNS
kubectl run dns-test --image=busybox --rm -it --restart=Never -- nslookup <svc>.<namespace>.svc.cluster.local

# 5. Check resolv.conf in a pod
kubectl exec <pod> -- cat /etc/resolv.conf
```

---

## 🔥 netstat (Legacy but Sometimes Available)

```bash
# Same as ss -tlnp
netstat -tlnp

# All connections
netstat -anp

# Count connections by state
netstat -an | awk '{print $6}' | sort | uniq -c | sort -rn
```

> 💡 Use `ss` instead of `netstat` — it's faster and always available.

---

## ⚡ CKA Troubleshooting Flows

### Service Not Reachable

```bash
# 1. Is the pod running?
kubectl get pods -l <selector>

# 2. Is the service endpoint populated?
kubectl get endpoints <svc-name>

# 3. Can you reach the pod directly?
curl --connect-timeout 3 http://<pod-ip>:<port>

# 4. Is something listening on the expected port (from the node)?
ss -tlnp | grep <port>

# 5. DNS working?
kubectl run tmp --image=busybox --rm -it --restart=Never -- nslookup <svc>.<ns>
```

### Node Network Issues

```bash
# Check interfaces
ip -br addr

# Check routes
ip route

# Check if node can reach API server
curl -k --connect-timeout 3 https://<api-server-ip>:6443/healthz

# Check if specific port is open
ss -tlnp | grep <port>
```

---

## 🎯 CKA Exam Patterns

| Scenario | Command |
|----------|---------|
| Check if port is listening | `ss -tlnp \| grep <port>` |
| Get node IP | `ip -br addr` or `ip addr show eth0` |
| Check default gateway | `ip route \| grep default` |
| Test DNS resolution | `k run tmp --image=busybox --rm -it --restart=Never -- nslookup svc.ns` |
| Check pod CIDR routes | `ip route` |
| Find what process uses a port | `ss -tlnp \| grep :<port>` |
| Check CoreDNS | `kubectl get pods -n kube-system -l k8s-app=kube-dns` |
| Test service connectivity | `curl --connect-timeout 3 http://cluster-ip:port` |

---

## 💡 Pro Tips

1. **`ss -tlnp`** — memorize this as THE network debug command (TCP, Listening, Numeric, Process)
2. **`ip -br addr`** — brief format is MUCH easier to read than `ip addr`
3. **`ip route get <IP>`** — tells you exactly which interface/route reaches that IP
4. **DNS format:** `<svc>.<namespace>.svc.cluster.local` — always use FQDN for cross-namespace
5. **`--connect-timeout 3`** on curl — don't waste exam time waiting for timeouts
6. **CoreDNS = `kube-dns` service** — the service is called `kube-dns` not `coredns`
7. **Check endpoints** — if service has no endpoints, the pod selector is wrong

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->