> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🖥️ tmux — Terminal Multiplexer for CKA

> **What it does:** Splits one terminal into multiple panes/windows. Run commands side-by-side.  
> **Why CKA cares:** CKA gives you ONE terminal. tmux lets you watch logs in one pane while editing in another.

---

## 🧠 Core Concept

```
tmux session
    └── Window 1 (like a browser tab)
        ├── Pane 1 (left)  → editing YAML
        └── Pane 2 (right) → watching logs
    └── Window 2
        └── Pane 1 → different context/node
```

**Prefix key:** `Ctrl+b` — you press this FIRST, then the command key.

---

## 🔥 Essential tmux Commands

### Session Management (from shell)

| Command | Action |
|---------|--------|
| `tmux` | Start new session |
| `tmux new -s cka` | New session named "cka" |
| `tmux ls` | List sessions |
| `tmux attach` or `tmux a` | Attach to last session |
| `tmux a -t cka` | Attach to session "cka" |
| `tmux kill-session -t cka` | Kill session |

### Inside tmux (Prefix = Ctrl+b)

#### Pane Management (MOST IMPORTANT)

| Keys | Action |
|------|--------|
| `Ctrl+b %` | Split pane **vertically** (left/right) |
| `Ctrl+b "` | Split pane **horizontally** (top/bottom) |
| `Ctrl+b ←↑→↓` | Move between panes (arrow keys) |
| `Ctrl+b x` | Kill current pane |
| `Ctrl+b z` | **Zoom** pane (toggle fullscreen) |
| `Ctrl+b Ctrl+←→` | Resize pane |
| `Ctrl+b q` | Show pane numbers |
| `Ctrl+b q <N>` | Switch to pane N |
| `Ctrl+b !` | Convert pane to window |

#### Window Management

| Keys | Action |
|------|--------|
| `Ctrl+b c` | Create new window |
| `Ctrl+b n` | Next window |
| `Ctrl+b p` | Previous window |
| `Ctrl+b <N>` | Go to window N (0-9) |
| `Ctrl+b ,` | Rename window |
| `Ctrl+b &` | Kill window |
| `Ctrl+b w` | List all windows (interactive) |

#### Session Commands

| Keys | Action |
|------|--------|
| `Ctrl+b d` | Detach from session (session keeps running) |
| `Ctrl+b s` | List/switch sessions |
| `Ctrl+b $` | Rename session |

#### Scroll Mode (Copy Mode)

| Keys | Action |
|------|--------|
| `Ctrl+b [` | Enter scroll/copy mode |
| `q` | Exit scroll mode |
| `↑↓` or `PgUp/PgDn` | Scroll in copy mode |
| `/pattern` | Search in scroll mode |

---

## ⚡ CKA Speed Workflow

### Setup at Exam Start

```bash
# Start tmux
tmux new -s exam

# Split into two panes (left for work, right for logs/monitoring)
# Press: Ctrl+b %

# Left pane: your main workspace
# Right pane: watching logs or monitoring
```

### The 2-Pane Troubleshooting Setup

```
┌──────────────────────┬──────────────────────┐
│                      │                      │
│  Main work:          │  Monitoring:         │
│  - vim pod.yaml      │  - journalctl -f     │
│  - kubectl apply     │  - kubectl get pods  │
│  - kubectl describe  │    -w                │
│                      │                      │
└──────────────────────┴──────────────────────┘

Ctrl+b %     → Split vertically
Ctrl+b ←→    → Switch between panes
Ctrl+b z     → Zoom into one pane (toggle)
```

### Practical CKA Scenarios

#### Scenario 1: Fix kubelet while watching logs
```
Left pane:   vim /var/lib/kubelet/config.yaml
             systemctl restart kubelet
Right pane:  journalctl -u kubelet -f
```

#### Scenario 2: Apply YAML while watching pods
```
Left pane:   vim deploy.yaml
             kubectl apply -f deploy.yaml
Right pane:  kubectl get pods -w
```

#### Scenario 3: SSH to worker node
```
Left pane:   Working on control plane
Right pane:  ssh node01 (working on worker node)
```

---

## 🎯 The Only tmux You Need for CKA

Honestly, for CKA you only need these 5 commands:

```
Ctrl+b %       → Split vertical
Ctrl+b "       → Split horizontal  
Ctrl+b ←→↑↓   → Switch panes
Ctrl+b z       → Zoom/unzoom pane
Ctrl+b x       → Close pane
```

That's it. Don't overcomplicate it.

---

## 💡 Pro Tips

1. **`Ctrl+b z` is your best friend** — zoom into a pane for full-screen work, zoom out to see both
2. **Use 2 panes max** — more than 2 gets confusing under exam pressure
3. **Right pane = monitoring** — always have `kubectl get pods -w` or `journalctl -f` running
4. **If you accidentally close tmux** — `tmux a` to reattach (your session is still alive)
5. **Don't waste exam time setting up tmux** — keep it simple: one split, that's it
6. **Ctrl+b [** then scroll — when you need to see output that scrolled past

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->