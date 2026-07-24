> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🗺️ Linux Tools for CKA — Master Index

> Your complete toolkit for the CKA exam. Each file = one tool, fully detailed.

---

## 📂 File Index

| # | File | Tool | Purpose |
|---|------|------|---------|
| 01 | [journalctl](./01-journalctl.md) | `journalctl` | Query systemd logs — kubelet, containerd, etcd debugging |
| 02 | [systemctl](./02-systemctl.md) | `systemctl` | Service lifecycle — start, stop, restart, status, enable |
| 03 | [vim](./03-vim.md) | `vim` | YAML editing — navigation, shortcuts, indentation |
| 04 | [tmux](./04-tmux.md) | `tmux` | Terminal multiplexer — split panes for parallel work |
| 05 | [sed](./05-sed.md) | `sed` | Stream editor — inline find-and-replace without opening vim |
| 06 | [awk](./06-awk.md) | `awk` | Text processing — column extraction from command output |
| 07 | [grep & pipes](./07-grep-and-pipes.md) | `grep`, `\|` | Pattern search + command chaining |
| 08 | [curl & wget](./08-curl-wget.md) | `curl`, `wget` | Network debugging — test services, APIs, connectivity |
| 09 | [openssl](./09-openssl.md) | `openssl` | Certificate inspection — expiry, SANs, chain verification |
| 10 | [crictl](./10-crictl.md) | `crictl` | Container runtime CLI — debug when kubectl can't reach pods |
| 11 | [etcdctl](./11-etcdctl.md) | `etcdctl` | etcd backup & restore — snapshot, verify, restore |
| 12 | [jq](./12-jq.md) | `jq` | JSON processing — parse kubectl JSON output |
| 13 | [find & xargs](./13-find-xargs.md) | `find`, `xargs` | File discovery — locate configs, certs, manifests |
| 14 | [network tools](./14-network-tools.md) | `ss`, `ip`, `nslookup` | Network troubleshooting — ports, routes, DNS |
| 15 | [small utilities](./15-tail-head-watch-sort.md) | `tail`, `head`, `watch`, `sort`, `uniq`, `wc` | Output filtering, monitoring, counting |

---

## 🏗️ How to Use These Guides

### Daily Study
1. Pick 2-3 tools per day
2. Read the CKA Speed Combos section
3. Practice the commands on your cluster
4. Focus on the **Pro Tips** — they save the most time

### Before Exam
1. Review the **CKA Exam Patterns** table in each file
2. Make sure you can type the **top 5 commands** for each tool from memory
3. Practice the troubleshooting flows end-to-end

### During Exam
1. Set up vim + aliases ([exam speed tricks](../cka-revision/00-exam-speed-tricks.md))
2. Use tmux (2 panes max)
3. Follow the troubleshooting flow: `status` → `logs` → `fix` → `restart` → `verify`

---

## 🎯 Priority Ranking (Most to Least Important for CKA)

### 🔴 MUST KNOW (Use in EVERY exam session)
1. **vim** — You edit YAML constantly
2. **systemctl** — Fix broken services
3. **journalctl** — Read error logs
4. **grep & pipes** — Filter everything
5. **curl/wget** — Test connectivity

### 🟡 HIGH VALUE (Use in many questions)
6. **etcdctl** — Backup/restore (high-probability question)
7. **openssl** — Certificate debugging
8. **crictl** — Container runtime issues
9. **ss/ip/nslookup** — Network debugging

### 🟢 NICE TO HAVE (Save time when used)
10. **sed** — Quick inline edits
11. **awk** — Column extraction
12. **jq** — Complex JSON parsing
13. **tmux** — Parallel panes
14. **find** — Locate files
15. **tail/sort/uniq** — Output processing

---

## 🧩 CKA Troubleshooting Master Flow

```
Problem detected
    │
    ├── Pod issue?
    │   ├── kubectl describe pod → check Events
    │   ├── kubectl logs pod → check app logs
    │   └── kubectl get events --sort-by=.lastTimestamp
    │
    ├── Node issue?
    │   ├── systemctl status kubelet → is it running?
    │   ├── journalctl -u kubelet -p err → what's the error?
    │   ├── systemctl status containerd → runtime ok?
    │   └── crictl ps -a → containers at runtime level?
    │
    ├── Network issue?
    │   ├── kubectl get endpoints → are endpoints populated?
    │   ├── nslookup/dig → DNS working?
    │   ├── curl/wget → can you reach the service?
    │   └── ss -tlnp → is something listening?
    │
    ├── Certificate issue?
    │   ├── openssl x509 -enddate → expired?
    │   ├── openssl x509 -text → SANs correct?
    │   ├── journalctl → x509 errors?
    │   └── kubeadm certs check-expiration
    │
    └── etcd issue?
        ├── etcdctl endpoint health
        ├── journalctl -u etcd
        └── etcdctl snapshot save/restore
```

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->