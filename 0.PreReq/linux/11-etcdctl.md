> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🗄️ etcdctl — etcd Backup & Restore

> **What it does:** CLI for interacting with etcd — the key-value store backing ALL Kubernetes state.  
> **Why CKA cares:** etcd backup/restore is a HIGH-PROBABILITY exam question. Know the exact commands.

---

## 🧠 Core Concept

```
etcd stores EVERYTHING:
  ├── Pods, Deployments, Services
  ├── ConfigMaps, Secrets
  ├── RBAC roles/bindings
  ├── Namespaces
  └── ALL cluster state

Lose etcd = Lose EVERYTHING
```

**etcd runs as:**
- **Static pod** (kubeadm clusters) → manifest at `/etc/kubernetes/manifests/etcd.yaml`
- **systemd service** (some setups) → `systemctl status etcd`

---

## 🔑 Required Environment Variable

```bash
# MUST set API version (etcdctl defaults to v2 which is WRONG)
export ETCDCTL_API=3
```

> 🚨 **CRITICAL:** Without `ETCDCTL_API=3`, all commands use v2 API and will fail or give wrong results.

---

## 🔥 Essential etcdctl Commands

### Backup (MEMORIZE THIS)

```bash
ETCDCTL_API=3 etcdctl snapshot save /tmp/etcd-backup.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

### Verify Backup

```bash
ETCDCTL_API=3 etcdctl snapshot status /tmp/etcd-backup.db --write-table
```

### Restore (MEMORIZE THIS)

```bash
ETCDCTL_API=3 etcdctl snapshot restore /tmp/etcd-backup.db \
  --data-dir=/var/lib/etcd-restored
```

After restore, update etcd to use new data dir:
```bash
# Edit etcd static pod manifest
vim /etc/kubernetes/manifests/etcd.yaml

# Change: --data-dir=/var/lib/etcd-restored
# Change: hostPath.path from /var/lib/etcd to /var/lib/etcd-restored
```

### Health Check

```bash
ETCDCTL_API=3 etcdctl endpoint health \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

### Member List

```bash
ETCDCTL_API=3 etcdctl member list \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --write-table
```

---

## ⚡ CKA Speed Trick: Find Cert Paths

Don't memorize cert paths — extract from etcd pod:

```bash
# Get all etcd flags from the static pod manifest
cat /etc/kubernetes/manifests/etcd.yaml | grep -E "cert|key|cacert|data-dir|endpoint"

# Or describe the pod
kubectl describe pod etcd-controlplane -n kube-system | grep -E "cert|key|ca"
```

---

## 🎯 CKA Exam Pattern: Full Backup-Restore Flow

```bash
# === BACKUP ===
# 1. Set API version
export ETCDCTL_API=3

# 2. Take snapshot
etcdctl snapshot save /opt/etcd-backup.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# 3. Verify
etcdctl snapshot status /opt/etcd-backup.db --write-table

# === RESTORE ===
# 4. Restore to new directory
etcdctl snapshot restore /opt/etcd-backup.db \
  --data-dir=/var/lib/etcd-from-backup

# 5. Update etcd manifest
vim /etc/kubernetes/manifests/etcd.yaml
# Change --data-dir to /var/lib/etcd-from-backup
# Change volumes/volumeMounts hostPath to /var/lib/etcd-from-backup

# 6. Wait for etcd to restart (it's a static pod)
crictl ps | grep etcd
# or
kubectl get pods -n kube-system | grep etcd
```

---

## 💡 Pro Tips

1. **ALWAYS `export ETCDCTL_API=3`** — put this first, every time
2. **Get cert paths from the manifest** — `grep` the etcd.yaml instead of memorizing
3. **Backup path matters** — exam usually tells you WHERE to save. Follow instructions exactly.
4. **After restore, change BOTH** `--data-dir` AND the `hostPath` volume in etcd.yaml
5. **Static pod restarts automatically** — editing `/etc/kubernetes/manifests/etcd.yaml` triggers kubelet to restart it
6. **Wait after restore** — etcd takes a few seconds to come back. Check with `crictl ps | grep etcd`

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->