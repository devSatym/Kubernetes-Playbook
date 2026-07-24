> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# ✏️ Vim — The CKA YAML Editor

> **What it does:** Terminal text editor. The ONLY editor available in CKA exam (nano may work but vim is faster).  
> **Why CKA cares:** You'll edit YAML files 50+ times during the exam. Speed here = time saved = more questions answered.

---

## 🔧 First: Set Up ~/.vimrc (Do This FIRST in Exam)

```bash
cat >> ~/.vimrc << 'EOF'
set tabstop=2
set shiftwidth=2
set expandtab
set number
set autoindent
set cursorline
set hlsearch
set paste
EOF
```

| Setting | Why |
|---------|-----|
| `tabstop=2` | YAML needs 2-space indentation |
| `shiftwidth=2` | `>>` and `<<` shift by 2 spaces |
| `expandtab` | Tabs → spaces (YAML HATES tabs) |
| `number` | Line numbers (debugging indentation) |
| `autoindent` | New lines match current indentation |
| `cursorline` | Highlight current line |
| `hlsearch` | Highlight search matches |
| `paste` | Prevents auto-indent on paste |

---

## 🔥 Essential Vim Commands

### Navigation

| Key | Action |
|-----|--------|
| `h j k l` | Left, Down, Up, Right |
| `gg` | Go to FIRST line |
| `G` (Shift+G) | Go to LAST line |
| `<N>G` or `:<N>` | Go to line N (e.g., `42G`) |
| `0` | Start of line |
| `$` | End of line |
| `w` | Next word |
| `b` | Previous word |
| `Ctrl+d` | Page down |
| `Ctrl+u` | Page up |
| `%` | Jump to matching bracket |

### Editing

| Key | Action |
|-----|--------|
| `i` | Insert BEFORE cursor |
| `a` | Insert AFTER cursor |
| `A` | Insert at END of line |
| `o` | New line BELOW, enter insert mode |
| `O` | New line ABOVE, enter insert mode |
| `x` | Delete character under cursor |
| `dd` | Delete entire line |
| `D` | Delete from cursor to end of line |
| `<N>dd` | Delete N lines (e.g., `5dd`) |
| `cc` | Delete line + enter insert mode |
| `cw` | Delete word + enter insert mode |
| `r<char>` | Replace single character |
| `~` | Toggle case of character |

### Copy/Paste

| Key | Action |
|-----|--------|
| `yy` | Yank (copy) line |
| `<N>yy` | Yank N lines |
| `p` | Paste BELOW current line |
| `P` | Paste ABOVE current line |
| `dd` then `p` | Cut + Paste (move a line) |

### Undo/Redo

| Key | Action |
|-----|--------|
| `u` | Undo |
| `Ctrl+r` | Redo |
| `.` | Repeat last command |

### Search

| Key | Action |
|-----|--------|
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` | Next match |
| `N` | Previous match |
| `*` | Search for word under cursor |
| `:noh` | Clear search highlighting |

### Find & Replace (CRITICAL for YAML)

```vim
:%s/old/new/g        " Replace ALL occurrences in file
:%s/old/new/gc       " Replace ALL with confirmation
:s/old/new/g         " Replace in current line only
:10,20s/old/new/g    " Replace in lines 10-20
```

### Save & Quit

| Key | Action |
|-----|--------|
| `:w` | Save |
| `:q` | Quit |
| `:wq` or `ZZ` | Save + Quit |
| `:q!` | Quit WITHOUT saving |
| `:wq!` | Force save + quit |

---

## ⚡ CKA YAML Speed Tricks

### Indent/Unindent Blocks (MOST USEFUL)

```vim
" Select lines visually, then shift:
V                     " Enter visual LINE mode
jjj                   " Select multiple lines
>                     " Indent all selected (2 spaces with our config)
<                     " Unindent all selected

" Repeat indent: after V-selecting and pressing >, press . to repeat
```

### Quick Indent Without Visual Mode

```vim
>>                    " Indent current line
<<                    " Unindent current line
5>>                   " Indent 5 lines from cursor
5<<                   " Unindent 5 lines from cursor
```

### Delete Everything in a File

```vim
gg                    " Go to top
dG                    " Delete to end
```

### Copy YAML Block from Docs → File

```bash
# In exam: copy from K8s docs, then in vim:
:set paste            " Prevents auto-indent mangling
i                     " Enter insert mode
Ctrl+Shift+V          " Paste from clipboard
Esc                   " Exit insert mode
:set nopaste          " Re-enable auto-indent
```

### Duplicate a Container Block

```vim
# Position on first line of container spec
V                     " Visual line mode
/- name               " Jump to next container (or wherever block ends)
k                     " Go up one line (to not include next block)
y                     " Yank selected
p                     " Paste below
```

### Quick Name Changes

```vim
# Change all "nginx" to "redis" in the file
:%s/nginx/redis/g

# Change namespace
:%s/default/production/g
```

---

## 🎯 CKA Workflow with Vim

### Generate → Edit → Apply

```bash
# 1. Generate YAML
k run nginx --image=nginx $do > pod.yaml

# 2. Edit in vim
vim pod.yaml

# 3. Make changes, save
:wq

# 4. Apply
k apply -f pod.yaml
```

### Common YAML Edits in CKA

```vim
" Add a label:
/metadata                 " Find metadata section
/labels                   " Find labels
o                         " New line below
  tier: frontend          " Type the label (2-space indent!)

" Add a volume mount:
/containers               " Find containers
/volumeMounts             " If exists, add to it; if not:
o                         " New line
    volumeMounts:
    - name: data
      mountPath: /data

" Add resource limits:
/image                    " Find image line
o                         " New line below
    resources:
      requests:
        cpu: "100m"
        memory: "128Mi"
      limits:
        cpu: "200m"
        memory: "256Mi"
```

---

## 💡 Pro Tips

1. **`:set paste` before pasting** from clipboard — prevents indentation disaster
2. **`V` + `>` or `<`** — fastest way to fix YAML indentation
3. **`:%s/old/new/g`** — instant rename across entire file
4. **`u` is your safety net** — undo anything, anytime
5. **`5dd`** — delete 5 lines at once (faster than line by line)
6. **`.` (dot)** — repeats your last command. After `>>`, press `.` to indent again
7. **`ZZ`** — faster than `:wq` (save + quit in 2 keys)
8. **`/<pattern>` then `n`** — jump through YAML sections fast

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->