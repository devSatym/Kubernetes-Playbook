> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🔍 grep & Pipes — Pattern Searching & Command Chaining

> **What it does:** grep finds text patterns. Pipes (`|`) chain commands. Together = powerful filtering.  
> **Why CKA cares:** Every debugging session uses grep. Finding errors in logs, filtering kubectl output, searching config files.

---

## 🔥 grep Essential Options

| Flag | Meaning | Example |
|------|---------|---------|
| `-i` | Case-insensitive | `grep -i error log.txt` |
| `-r` or `-R` | Recursive search in directory | `grep -r "apiserver" /etc/kubernetes/` |
| `-n` | Show line numbers | `grep -n "image:" pod.yaml` |
| `-l` | Show only filenames (not content) | `grep -rl "nginx" /manifests/` |
| `-c` | Count matches | `grep -c "error" log.txt` |
| `-v` | **Invert** — show NON-matching lines | `grep -v "Running"` |
| `-A N` | Show N lines AFTER match | `grep -A 5 "containers:" pod.yaml` |
| `-B N` | Show N lines BEFORE match | `grep -B 3 "error" log.txt` |
| `-C N` | Show N lines BEFORE and AFTER | `grep -C 2 "error" log.txt` |
| `-w` | Match whole word only | `grep -w "pod" file` (not "pods") |
| `-E` | Extended regex (same as `egrep`) | `grep -E "error|warning|fatal"` |
| `--color` | Highlight matches | `grep --color "error" log.txt` |

---

## ⚡ CKA Speed Combos

### Find Errors in Logs
```bash
# Find errors in kubelet logs
journalctl -u kubelet --no-pager | grep -i "error\|failed\|unable"

# Find certificate issues
journalctl -u kubelet --no-pager | grep -i "x509\|cert\|expired"

# Find OOM kills
journalctl -k --no-pager | grep -i "oom\|out of memory"

# Find CNI/network issues
journalctl -u kubelet --no-pager | grep -i "cni\|network\|calico\|flannel"
```

### Search Kubernetes Configs
```bash
# Find which manifest uses a specific image
grep -rn "image:" /etc/kubernetes/manifests/

# Find which file has a specific setting
grep -rl "advertise-address" /etc/kubernetes/

# Find all volumeMount references
grep -rn "volumeMount\|hostPath\|emptyDir" /etc/kubernetes/manifests/

# Find where a port is configured
grep -rn "port:" /etc/kubernetes/manifests/ | grep -v "#"
```

### Filter kubectl Output
```bash
# Find non-running pods
kubectl get pods -A | grep -v "Running\|Completed"

# Find pods in CrashLoopBackOff
kubectl get pods -A | grep "CrashLoop"

# Find pods with restarts > 0
kubectl get pods -A | grep -v "0         "

# Find events with warnings
kubectl get events -A | grep -i "warning\|error\|failed"
```

### YAML Field Search
```bash
# Find where "resources" is defined in a file
grep -n "resources:" deploy.yaml

# Show container specs (name + image)
grep -E "name:|image:" pod.yaml

# Find all files with networkpolicy
grep -rl "kind: NetworkPolicy" /manifests/
```

---

## 🔗 Pipe Patterns (|)

### Basic Chaining
```bash
# Chain any commands
command1 | command2 | command3

# Output of command1 → input of command2 → input of command3
```

### Essential Pipe Combos for CKA

```bash
# Sort and count
kubectl get pods -A --no-headers | awk '{print $3}' | sort | uniq -c | sort -rn
# Result: count of pods by status

# Find + count errors
journalctl -u kubelet --no-pager | grep -c "error"

# Get unique error messages
journalctl -u kubelet -p err --no-pager -o cat | sort -u

# Filter → extract → sort
kubectl get pods -A --no-headers | grep -v Running | awk '{print $1, $2, $4}' | sort

# Watch specific output
watch "kubectl get pods | grep -v Running"

# Find a process
ps aux | grep kubelet | grep -v grep

# Count lines
kubectl get pods --no-headers | wc -l

# First/last N results
kubectl get events --sort-by=.lastTimestamp | head -20
kubectl get events --sort-by=.lastTimestamp | tail -10
```

### Multi-grep (AND logic)
```bash
# Lines containing BOTH "error" AND "kubelet"
grep "error" log.txt | grep "kubelet"

# Lines containing "error" OR "warning"
grep -E "error|warning" log.txt
```

### grep + xargs
```bash
# Find all YAML files containing "nginx" and list them
grep -rl "nginx" /manifests/ | xargs ls -la

# Find and delete all pods with "test" in name
kubectl get pods --no-headers | awk '{print $1}' | grep "test" | xargs kubectl delete pod
```

---

## 🎯 CKA Exam Patterns

| Scenario | Command |
|----------|---------|
| Find broken pods | `kubectl get pods -A \| grep -v "Running\|Completed"` |
| Search config files | `grep -rn "pattern" /etc/kubernetes/` |
| Find error logs | `journalctl -u kubelet --no-pager \| grep -i error` |
| Count resources | `kubectl get pods --no-headers \| wc -l` |
| Find YAML key | `grep -n "key:" file.yaml` |
| Show context around match | `grep -C 3 "error" log` |
| Exclude pattern | `grep -v "pattern"` |

---

## 💡 Pro Tips

1. **`grep -v Running`** — fastest way to find broken pods in `kubectl get pods` output
2. **`grep -r` in `/etc/kubernetes/`** — find ANY config value across all K8s configs
3. **`grep -A 5`** — show context AFTER a match (great for YAML structure)
4. **`grep -E "a|b|c"`** — match multiple patterns at once (OR logic)
5. **`| head -20`** — limit output to prevent scrolling through thousands of lines
6. **`| sort | uniq -c | sort -rn`** — the "count and rank" pipeline (very useful for log analysis)
7. **`grep -v grep`** — when doing `ps aux | grep process`, exclude the grep itself

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->