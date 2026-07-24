> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🛠️ tail, head, watch, wc, sort, uniq — Small Utilities, Big Impact

> **What they do:** Small single-purpose tools that you chain together with pipes.  
> **Why CKA cares:** You use these CONSTANTLY for filtering output, monitoring, and counting.

---

## 🔥 tail — View End of Output

| Command | What It Does |
|---------|-------------|
| `tail file` | Last 10 lines |
| `tail -n 20 file` | Last 20 lines |
| `tail -f file` | **Follow** — live updates (like journalctl -f) |
| `tail -f /var/log/syslog` | Watch syslog live |
| `command \| tail -5` | Last 5 lines of command output |

### CKA Usage
```bash
# Last 20 lines of kubelet log
journalctl -u kubelet --no-pager | tail -20

# Follow a log file
tail -f /var/log/containers/kube-apiserver*.log

# Last 5 events
kubectl get events --sort-by=.lastTimestamp | tail -5
```

---

## 🔥 head — View Start of Output

| Command | What It Does |
|---------|-------------|
| `head file` | First 10 lines |
| `head -n 5 file` | First 5 lines |
| `command \| head -20` | First 20 lines of output |

### CKA Usage
```bash
# First 10 errors from kubelet
journalctl -u kubelet -p err --no-pager | head -10

# Quick peek at a YAML file
head -30 deploy.yaml
```

---

## 🔥 watch — Repeat Command Every N Seconds

| Command | What It Does |
|---------|-------------|
| `watch <command>` | Run command every 2 seconds |
| `watch -n 1 <command>` | Run every 1 second |
| `watch -d <command>` | Highlight differences between runs |

### CKA Usage
```bash
# Watch pods continuously
watch kubectl get pods

# Watch with 1-second refresh
watch -n 1 kubectl get pods

# Watch specific namespace
watch "kubectl get pods -n kube-system"

# Watch nodes
watch kubectl get nodes

# Watch events
watch "kubectl get events --sort-by=.lastTimestamp | tail -10"
```

> 💡 **Alternative:** `kubectl get pods -w` (--watch flag) does the same thing natively.

---

## 🔥 wc — Word/Line Count

| Command | What It Does |
|---------|-------------|
| `wc -l` | Count lines |
| `wc -w` | Count words |
| `wc -c` | Count characters |

### CKA Usage
```bash
# Count number of pods
kubectl get pods --no-headers | wc -l

# Count number of nodes
kubectl get nodes --no-headers | wc -l

# Count errors in kubelet log
journalctl -u kubelet -p err --no-pager | wc -l

# Count running pods
kubectl get pods --no-headers | grep Running | wc -l
```

---

## 🔥 sort — Sort Output

| Flag | What It Does |
|------|-------------|
| `sort` | Alphabetical sort |
| `sort -n` | Numeric sort |
| `sort -r` | Reverse sort |
| `sort -rn` | Reverse numeric (highest first) |
| `sort -k2` | Sort by 2nd column |
| `sort -u` | Sort + unique (remove duplicates) |
| `sort -t: -k3 -n` | Sort by 3rd field with `:` delimiter |

### CKA Usage
```bash
# Sort pods by name
kubectl get pods --no-headers | sort

# Sort by status column
kubectl get pods --no-headers | sort -k3

# Unique error messages
journalctl -u kubelet -p err -o cat --no-pager | sort -u
```

---

## 🔥 uniq — Deduplicate (Use After sort)

| Command | What It Does |
|---------|-------------|
| `sort \| uniq` | Remove duplicates |
| `sort \| uniq -c` | Count duplicates |
| `sort \| uniq -c \| sort -rn` | **Rank by frequency** |

### CKA Usage
```bash
# Top error messages from kubelet
journalctl -u kubelet -p err -o cat --no-pager | sort | uniq -c | sort -rn | head -10

# Count pods per status
kubectl get pods --no-headers | awk '{print $3}' | sort | uniq -c | sort -rn

# Count pods per namespace
kubectl get pods -A --no-headers | awk '{print $1}' | sort | uniq -c | sort -rn
```

---

## ⚡ Power Pipelines (Combining Everything)

```bash
# Top 5 most common kubelet errors
journalctl -u kubelet --no-pager -o cat | grep -i error | sort | uniq -c | sort -rn | head -5

# Non-running pods, sorted
kubectl get pods -A --no-headers | grep -v Running | sort -k4 -rn

# Count resources by namespace
kubectl get all -A --no-headers | awk '{print $1}' | sort | uniq -c | sort -rn

# Find most restarted pods
kubectl get pods --no-headers | sort -k4 -rn | head -5
```

---

## 🎯 Quick Reference

| Need | Command |
|------|---------|
| Last N lines | `\| tail -N` |
| First N lines | `\| head -N` |
| Live monitoring | `watch "command"` or `\| tail -f` |
| Count lines | `\| wc -l` |
| Sort by column | `\| sort -kN` |
| Count & rank | `\| sort \| uniq -c \| sort -rn` |
| Remove duplicates | `\| sort -u` |

---

## 💡 Pro Tips

1. **`| tail -20`** — prevents overwhelming output (use after grep/awk)
2. **`watch -n 1 kubectl get pods`** — better than repeatedly typing the command
3. **`sort | uniq -c | sort -rn`** — THE pattern for "show me the most common X"
4. **`wc -l`** — instant count of anything
5. **`kubectl get pods -w`** — native watch (simpler than `watch kubectl get pods`)
6. **`head -30 file.yaml`** — quick peek without opening vim

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->