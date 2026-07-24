> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# ⚙️ systemctl — Service Lifecycle Manager

> **What it does:** Controls systemd services — start, stop, restart, enable, disable, check status.  
> **Why CKA cares:** kubelet, containerd run as systemd services. You MUST know how to manage them.

---

## 🧠 Core Concept

```
systemd = init system (PID 1) manages ALL services
    ├── kubelet.service      ← ALWAYS systemd
    ├── containerd.service   ← ALWAYS systemd  
    ├── etcd.service         ← Sometimes systemd, sometimes static pod
    └── kube-apiserver       ← Sometimes systemd, sometimes static pod
```

**Unit file locations:**
- `/etc/systemd/system/` — Custom/override (HIGHEST PRIORITY)
- `/usr/lib/systemd/system/` — Package-installed
- `/lib/systemd/system/` — Symlink to above on most distros

---

## 🔥 Essential Commands

### Service Lifecycle

| Command | What It Does |
|---------|-------------|
| `systemctl start <svc>` | Start the service |
| `systemctl stop <svc>` | Stop the service |
| `systemctl restart <svc>` | Stop + Start |
| `systemctl enable <svc>` | Start on boot |
| `systemctl enable --now <svc>` | Enable + Start immediately (**best combo**) |
| `systemctl daemon-reload` | **Reload after editing .service files** |

### Status & Inspection

| Command | What It Does |
|---------|-------------|
| `systemctl status <svc>` | Show status + last log lines (**FIRST CHECK**) |
| `systemctl is-active <svc>` | Quick: `active` or `inactive` |
| `systemctl is-enabled <svc>` | Quick: `enabled` or `disabled` |
| `systemctl list-units --state=failed` | **Find ALL broken services** |
| `systemctl cat <svc>` | Show unit file + all drop-ins |
| `systemctl show <svc> -p MainPID` | Get specific property |

---

## ⚡ CKA Speed Combos

### 🔴 Fix a Broken kubelet
```bash
systemctl status kubelet                    # 1. See what's wrong
journalctl -u kubelet -n 50 -p err         # 2. Get error details
# ... fix the issue ...
systemctl daemon-reload                     # 3. Reload config
systemctl restart kubelet                   # 4. Restart
systemctl status kubelet                    # 5. Verify
```

### 🔴 Find ALL Failed Services
```bash
systemctl list-units --type=service --state=failed
```

### 🔴 Check kubelet Config
```bash
systemctl cat kubelet                       # Full config + drop-ins
systemctl cat kubelet | grep ExecStart      # See command + flags
ls /etc/systemd/system/kubelet.service.d/   # Drop-in directory
```

---

## 🧩 kubelet systemd Layout (kubeadm)

```
/lib/systemd/system/kubelet.service           ← Main unit file
/etc/systemd/system/kubelet.service.d/        ← Drop-in directory
    └── 10-kubeadm.conf                       ← Sets --config, --kubeconfig
```

**Key kubelet paths (memorize):**

| Path | Purpose |
|------|---------|
| `/var/lib/kubelet/config.yaml` | kubelet config |
| `/etc/kubernetes/kubelet.conf` | kubelet kubeconfig |
| `/etc/kubernetes/pki/` | Certificates |
| `/etc/kubernetes/manifests/` | Static pod manifests |

---

## 🔄 The Holy Trinity (Muscle Memory)

```bash
systemctl daemon-reload    # 1. Tell systemd "config changed"
systemctl restart kubelet  # 2. Restart with new config  
systemctl status kubelet   # 3. Verify running
```

> 🚨 Forgetting `daemon-reload` after editing a `.service` file = changes IGNORED = #1 CKA mistake.

---

## 🎯 CKA Exam Patterns

| Scenario | Commands |
|----------|----------|
| Node NotReady | `systemctl status kubelet` → logs → fix → restart |
| kubelet not starting | `systemctl cat kubelet` → check ExecStart → fix → daemon-reload → restart |
| After kubeadm upgrade | `systemctl daemon-reload && systemctl restart kubelet` |
| Container runtime issues | `systemctl status containerd` → restart |
| Check boot start | `systemctl is-enabled kubelet` |
| Find broken services | `systemctl list-units --state=failed` |

---

## 💡 Pro Tips

1. **`systemctl status` is diagnostic entry point** — shows active/inactive, last logs, PID, command
2. **`systemctl cat` > manually finding files** — shows COMPLETE config with all drop-ins
3. **NEVER forget `daemon-reload`** after editing any `.service` or drop-in file
4. **`enable --now`** = enable + start in one command
5. **`--state=failed`** = fastest way to find broken things on a node

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->