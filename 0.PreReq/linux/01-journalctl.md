> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 📋 journalctl — The CKA Log Investigation Weapon

> **What it does:** Queries the systemd journal — the unified logging system for ALL systemd-managed services.  
> **Why CKA cares:** Kubelet, containerd, etcd, kube-apiserver (when running as systemd services) all log here. When a node is broken, journalctl is your FIRST tool.

---

## 🧠 Core Concept

systemd captures stdout/stderr of every service it manages → stores in binary journal → `journalctl` reads it.

```
Service (kubelet, containerd, etcd)
    │
    ▼
systemd captures stdout/stderr
    │
    ▼
Binary Journal (/var/log/journal/ or /run/log/journal/)
    │
    ▼
journalctl reads & filters it
```

---

## 🔥 Essential Options Reference

### Unit Filtering (MOST IMPORTANT for CKA)

| Flag | Meaning | Example |
|------|---------|---------|
| `-u <unit>` | Show logs for a specific systemd unit | `journalctl -u kubelet` |
| `-u <unit1> -u <unit2>` | Show logs for multiple units | `journalctl -u kubelet -u containerd` |

### Time Filtering

| Flag | Meaning | Example |
|------|---------|---------|
| `--since "time"` | Logs after this time | `journalctl --since "5 min ago"` |
| `--until "time"` | Logs before this time | `journalctl --until "2024-01-01 12:00"` |
| `--since "today"` | Logs from today only | `journalctl --since "today"` |
| `--since "1 hour ago"` | Last 1 hour of logs | `journalctl -u kubelet --since "1 hour ago"` |

### Output Control

| Flag | Meaning | Example |
|------|---------|---------|
| `-f` | **Follow** (like `tail -f`) — LIVE logs | `journalctl -u kubelet -f` |
| `-n <N>` | Show last N lines (default 10) | `journalctl -u kubelet -n 50` |
| `--no-pager` | Dump all output (no `less`) | `journalctl -u kubelet --no-pager` |
| `-o json-pretty` | JSON output (parseable) | `journalctl -u kubelet -o json-pretty` |
| `-o short-iso` | Timestamps in ISO format | `journalctl -u kubelet -o short-iso` |
| `-o cat` | **Plain message only** — no metadata | `journalctl -u kubelet -o cat` |

### Priority Filtering

| Flag | Meaning | Example |
|------|---------|---------|
| `-p err` | Only errors and above | `journalctl -u kubelet -p err` |
| `-p warning` | Warnings and above | `journalctl -u kubelet -p warning` |
| `-p crit` | Critical only | `journalctl -p crit` |

Priority levels: `emerg(0)` > `alert(1)` > `crit(2)` > `err(3)` > `warning(4)` > `notice(5)` > `info(6)` > `debug(7)`

### Boot Filtering

| Flag | Meaning | Example |
|------|---------|---------|
| `-b` | Current boot only | `journalctl -u kubelet -b` |
| `-b -1` | Previous boot | `journalctl -b -1` |
| `--list-boots` | List all recorded boots | `journalctl --list-boots` |

### Kernel Logs

| Flag | Meaning | Example |
|------|---------|---------|
| `-k` | Kernel messages only (like `dmesg`) | `journalctl -k` |
| `-k -b` | Kernel messages this boot | `journalctl -k -b` |

### Reverse (Newest First)

| Flag | Meaning | Example |
|------|---------|---------|
| `-r` | Reverse order (newest first) | `journalctl -u kubelet -r -n 20` |

---

## ⚡ CKA Speed Combos (Copy-Paste Ready)

### 🔴 Node Not Ready — Investigate kubelet
```bash
# Step 1: Check kubelet status
systemctl status kubelet

# Step 2: Last 50 error lines from kubelet
journalctl -u kubelet -n 50 -p err --no-pager

# Step 3: Follow live kubelet logs (while restarting)
journalctl -u kubelet -f

# Step 4: Search for specific error pattern
journalctl -u kubelet --no-pager | grep -i "failed\|error\|unable"
```

### 🔴 Container Runtime Issues
```bash
# Check containerd logs
journalctl -u containerd -n 100 --no-pager

# Errors only from containerd
journalctl -u containerd -p err --no-pager

# Both kubelet + containerd together
journalctl -u kubelet -u containerd --since "10 min ago" --no-pager
```

### 🔴 API Server Down (systemd-based setup)
```bash
# If apiserver is a systemd service
journalctl -u kube-apiserver -n 100 --no-pager

# Check for certificate errors
journalctl -u kube-apiserver --no-pager | grep -i "cert\|tls\|x509"
```

### 🔴 etcd Issues
```bash
# etcd logs
journalctl -u etcd -n 100 -p err --no-pager

# Check for disk/performance issues
journalctl -u etcd --no-pager | grep -i "slow\|took too long\|disk"
```

---

## 🧩 Smart Pipe Combos

### journalctl + grep (Find specific errors)
```bash
# Find OOM kills
journalctl -k --no-pager | grep -i "oom\|out of memory"

# Find kubelet crash reasons
journalctl -u kubelet --no-pager | grep -i "error\|fatal\|panic" | tail -20

# Find certificate problems
journalctl -u kubelet --no-pager | grep -i "x509\|certificate\|expired"
```

### journalctl + tail (Last N matching lines)
```bash
# Last 10 error lines from kubelet
journalctl -u kubelet --no-pager | grep -i error | tail -10
```

### journalctl + wc (Count errors)
```bash
# How many errors in kubelet since boot?
journalctl -u kubelet -b -p err --no-pager | wc -l
```

### journalctl + grep + cut (Extract specific fields)
```bash
# Get unique error messages from kubelet
journalctl -u kubelet -p err --no-pager -o cat | sort -u | head -20
```

---

## 🎯 CKA Exam Patterns — When to Use journalctl

| Scenario | Command |
|----------|---------|
| Node shows `NotReady` | `journalctl -u kubelet -n 50 -p err` |
| Pods stuck in `ContainerCreating` | `journalctl -u containerd -n 50 --no-pager` |
| API server not responding | `journalctl -u kube-apiserver -n 50` (if systemd) |
| etcd cluster unhealthy | `journalctl -u etcd -p err --no-pager` |
| Network plugin errors | `journalctl -u kubelet --no-pager \| grep -i "cni\|network"` |
| Certificate expired | `journalctl -u kubelet --no-pager \| grep -i "x509\|cert"` |
| After restarting a service | `journalctl -u kubelet -f` (watch live) |

---

## 💡 Pro Tips for CKA

1. **Always use `--no-pager` in exam** — avoids getting stuck in `less`, output goes straight to terminal
2. **`-o cat` is your friend** — strips timestamps/metadata, shows only the message. Easier to scan.
3. **Combine `-n` with `-p err`** — `journalctl -u kubelet -n 30 -p err` gives you the last 30 errors ONLY
4. **`-f` while restarting** — Open a second terminal (or tmux pane), run `journalctl -u kubelet -f`, then restart kubelet in the other pane
5. **`--since "5 min ago"`** — After making a fix, check only recent logs to see if the fix worked
6. **`-r` for newest first** — `journalctl -u kubelet -r -n 10` shows the LATEST errors at the top

---

## 🚨 Common Gotchas

- **Static pods (apiserver, scheduler, etc.) on kubeadm clusters log to container runtime, NOT systemd** → Use `crictl logs` or `kubectl logs` for those
- **kubelet ALWAYS runs as a systemd service** → `journalctl -u kubelet` always works
- **containerd ALWAYS runs as a systemd service** → `journalctl -u containerd` always works
- If `journalctl` shows nothing → service name might be different, try `systemctl list-units | grep kube`

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->