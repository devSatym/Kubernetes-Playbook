> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# ✂️ sed — Stream Editor for CKA

> **What it does:** Edits text in-place or in a stream. Find-and-replace without opening a file.  
> **Why CKA cares:** Quick inline edits to YAML/config files. Faster than opening vim for simple changes.

---

## 🧠 Core Syntax

```
sed [options] 'command' file
sed [options] 's/pattern/replacement/flags' file
```

---

## 🔥 Essential sed Commands

### Substitution (s command) — The Main Thing

| Command | What It Does |
|---------|-------------|
| `sed 's/old/new/' file` | Replace FIRST `old` on each line |
| `sed 's/old/new/g' file` | Replace ALL `old` on each line |
| `sed -i 's/old/new/g' file` | **In-place edit** (modifies the file!) |
| `sed 's/old/new/gi' file` | Case-insensitive replace |
| `sed '3s/old/new/' file` | Replace only on line 3 |
| `sed '1,5s/old/new/g' file` | Replace in lines 1-5 |

### Delete Lines (d command)

| Command | What It Does |
|---------|-------------|
| `sed '/pattern/d' file` | Delete lines matching pattern |
| `sed '5d' file` | Delete line 5 |
| `sed '1,3d' file` | Delete lines 1-3 |
| `sed '/^#/d' file` | Delete all comment lines |
| `sed '/^$/d' file` | Delete all blank lines |

### Print Lines (p command)

| Command | What It Does |
|---------|-------------|
| `sed -n '/pattern/p' file` | Print only matching lines (like grep) |
| `sed -n '5,10p' file` | Print lines 5-10 |

### Insert/Append (i/a commands)

| Command | What It Does |
|---------|-------------|
| `sed '3i\new line' file` | Insert "new line" BEFORE line 3 |
| `sed '3a\new line' file` | Append "new line" AFTER line 3 |
| `sed '/pattern/a\new line' file` | Append after matching lines |

---

## ⚡ CKA Speed Combos

### Quick YAML Edits Without Opening Vim

```bash
# Change image tag
sed -i 's/nginx:latest/nginx:1.25/g' deploy.yaml

# Change namespace
sed -i 's/namespace: default/namespace: production/g' manifest.yaml

# Change replicas
sed -i 's/replicas: 1/replicas: 3/' deploy.yaml

# Change port number
sed -i 's/containerPort: 80/containerPort: 8080/' pod.yaml
```

### Fix Config Files

```bash
# Fix kubelet config value
sed -i 's/staticPodPath: .*/staticPodPath: \/etc\/kubernetes\/manifests/' /var/lib/kubelet/config.yaml

# Change cluster DNS
sed -i 's/clusterDNS:.*/clusterDNS: ["10.96.0.10"]/' /var/lib/kubelet/config.yaml
```

### Remove Comments/Blank Lines from Output

```bash
# Clean view of a config file
sed '/^#/d; /^$/d' /etc/kubernetes/manifests/kube-apiserver.yaml
```

### Generate Multiple Files from Template

```bash
# Create 3 namespace YAML files from a template
for ns in dev staging prod; do
  sed "s/NAMESPACE/$ns/g" template.yaml > ${ns}-ns.yaml
done
```

### Pipe Combos

```bash
# Get kubelet errors, strip timestamps
journalctl -u kubelet --no-pager | sed 's/^.*kubelet\[.*\]: //' | grep -i error | tail -10

# Extract specific values from kubectl output
kubectl get nodes -o wide --no-headers | sed 's/  */ /g' | cut -d' ' -f1,6
```

---

## 🔑 Key Options

| Option | Meaning |
|--------|---------|
| `-i` | **In-place** — edit the file directly (CAREFUL!) |
| `-i.bak` | In-place + create backup (safer) |
| `-n` | Suppress default output (use with `p`) |
| `-e` | Multiple commands: `sed -e 's/a/b/' -e 's/c/d/'` |

---

## 🎯 CKA Exam Patterns

| Scenario | sed Command |
|----------|-------------|
| Change image in YAML | `sed -i 's/image: old/image: new/' file.yaml` |
| Change namespace | `sed -i 's/namespace: .*/namespace: newns/' file.yaml` |
| Quick config fix | `sed -i 's/wrongvalue/correctvalue/' config.yaml` |
| Remove comments from output | `sed '/^#/d' file` |
| Batch modify | `sed -i 's/old/new/g' *.yaml` |

---

## 💡 Pro Tips

1. **`sed -i` modifies the file** — no undo! Use `sed -i.bak` if unsure (creates backup)
2. **Use different delimiters for paths**: `sed 's|/old/path|/new/path|g'` (avoids escaping `/`)
3. **Faster than vim for single changes** — don't open vim just to change one word
4. **Works in pipes** — `cat file | sed 's/old/new/'` or `kubectl get ... | sed ...`
5. **`-e` for multiple edits**: `sed -i -e 's/a/b/' -e 's/c/d/' file`

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->