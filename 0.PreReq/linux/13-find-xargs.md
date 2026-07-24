> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 📁 find & xargs — File Discovery & Batch Operations

> **What they do:** `find` locates files. `xargs` passes found files to other commands.  
> **Why CKA cares:** Finding config files, manifests, certificates, and log files fast.

---

## 🔥 find Essential Options

### Basic Syntax
```bash
find <path> [options] [action]
```

### By Name

| Command | What It Does |
|---------|-------------|
| `find / -name "kubelet.conf"` | Find file by exact name |
| `find / -name "*.crt"` | Find all `.crt` files |
| `find / -iname "*.yaml"` | Case-insensitive name search |
| `find /etc -name "*.conf"` | Find .conf files under /etc |

### By Type

| Flag | Type |
|------|------|
| `-type f` | Regular files |
| `-type d` | Directories |
| `-type l` | Symbolic links |

```bash
# Find directories named "manifests"
find /etc -type d -name "manifests"

# Find only files (not dirs) named config
find /var -type f -name "config*"
```

### By Time (Recently Modified)

```bash
# Modified in last 10 minutes
find /etc/kubernetes -mmin -10

# Modified in last 1 day
find /etc/kubernetes -mtime -1

# Modified MORE than 7 days ago
find /etc/kubernetes -mtime +7
```

### By Size

```bash
# Files larger than 10MB
find /var/log -size +10M

# Empty files
find /tmp -empty
```

### By Content (find + grep)

```bash
# Find files containing a pattern
find /etc/kubernetes -name "*.yaml" -exec grep -l "image:" {} \;

# Faster with xargs
find /etc/kubernetes -name "*.yaml" | xargs grep -l "image:"
```

---

## ⚡ CKA Speed Combos

### Find Kubernetes Config Files
```bash
# Find all manifests
find /etc/kubernetes/manifests -name "*.yaml"

# Find kubelet config
find / -name "config.yaml" -path "*/kubelet/*" 2>/dev/null

# Find all kubeconfig files
find /etc/kubernetes -name "*.conf"

# Find all certificate files
find /etc/kubernetes/pki -name "*.crt"
```

### Find Recently Changed Files (After Debugging)
```bash
# What changed in the last 5 minutes?
find /etc/kubernetes -mmin -5 -type f

# What YAML files changed today?
find /etc/kubernetes -name "*.yaml" -mtime 0
```

### Find and View
```bash
# Find and cat all manifests
find /etc/kubernetes/manifests -name "*.yaml" -exec cat {} \;

# Find and check cert expiry
find /etc/kubernetes/pki -name "*.crt" -exec openssl x509 -enddate -noout -in {} \;
```

---

## 🔗 xargs Essentials

```bash
# Basic: pass find results to another command
find /manifests -name "*.yaml" | xargs kubectl apply -f

# With grep
find /etc/kubernetes -name "*.yaml" | xargs grep -l "hostPath"

# Handle spaces in filenames
find /path -name "*.yaml" -print0 | xargs -0 grep "pattern"

# One at a time
find /path -name "*.yaml" | xargs -I {} kubectl apply -f {}
```

---

## 🎯 CKA Exam Patterns

| Scenario | Command |
|----------|---------|
| Find K8s manifests | `find /etc/kubernetes/manifests -name "*.yaml"` |
| Find config files | `find /etc/kubernetes -name "*.conf"` |
| Find certs | `find /etc/kubernetes/pki -name "*.crt"` |
| Recent changes | `find /etc/kubernetes -mmin -5 -type f` |
| Search file contents | `find /etc -name "*.yaml" \| xargs grep "pattern"` |
| Apply all yamls | `find /manifests -name "*.yaml" \| xargs kubectl apply -f` |

---

## 💡 Pro Tips

1. **`2>/dev/null`** — suppress "Permission denied" errors: `find / -name "file" 2>/dev/null`
2. **`-path` for filtering** — `find / -name "config" -path "*/kubernetes/*"` narrows results
3. **`xargs grep -l`** — shows only FILENAMES containing the pattern (not the content)
4. **`-mmin -5`** — "what just changed?" after making edits
5. **`find /etc/kubernetes`** — almost everything K8s-related is under here
6. **`-exec {} \;`** vs `| xargs`** — xargs is faster for bulk operations

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->