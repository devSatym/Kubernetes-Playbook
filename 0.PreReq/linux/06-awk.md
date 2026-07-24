> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 📊 awk — Text Processing Powerhouse

> **What it does:** Pattern scanning + field extraction. Processes text column-by-column.  
> **Why CKA cares:** Extract specific columns from kubectl output, parse logs, format data.

---

## 🧠 Core Syntax

```
awk 'pattern { action }' file
command | awk '{ action }'
```

**Built-in variables:**
- `$0` = entire line
- `$1, $2, $3...` = 1st, 2nd, 3rd column (field)
- `NR` = line number (Number of Record)
- `NF` = number of fields on current line
- `$NF` = LAST field

Default field separator = whitespace.

---

## 🔥 Essential awk Commands

### Column Extraction (MOST USED)

```bash
# Print 1st column
awk '{print $1}' file

# Print 1st and 3rd columns
awk '{print $1, $3}' file

# Print last column
awk '{print $NF}' file

# Print with custom separator
awk '{print $1 ":" $2}' file
```

### With Patterns (Filter + Extract)

```bash
# Print lines containing "error"
awk '/error/ {print}' file

# Print column 1 of lines containing "Running"
awk '/Running/ {print $1}' file

# Print lines where column 3 equals "Ready"
awk '$3 == "Ready" {print $1}' file

# Print lines where column 4 > 0
awk '$4 > 0 {print $1, $4}' file
```

### Custom Field Separator

```bash
# Use ":" as separator (for /etc/passwd, etc.)
awk -F: '{print $1}' /etc/passwd

# Use comma as separator
awk -F, '{print $1, $2}' file.csv

# Multiple separators
awk -F'[:/]' '{print $1}' file
```

### Line Numbers

```bash
# Print with line numbers
awk '{print NR, $0}' file

# Print only line 5
awk 'NR==5' file

# Print lines 5-10
awk 'NR>=5 && NR<=10' file

# Skip header (line 1)
awk 'NR>1 {print $1}' file
```

---

## ⚡ CKA Speed Combos

### Extract Pod Names

```bash
# Get just pod names
kubectl get pods --no-headers | awk '{print $1}'

# Get pod names that are NOT Running
kubectl get pods --no-headers | awk '$3 != "Running" {print $1, $3}'

# Get pod names with restart count > 0
kubectl get pods --no-headers | awk '$4 > 0 {print $1, $4}'
```

### Extract Node Info

```bash
# Get node names only
kubectl get nodes --no-headers | awk '{print $1}'

# Get NotReady nodes
kubectl get nodes --no-headers | awk '$2 != "Ready" {print $1}'

# Get node IPs
kubectl get nodes -o wide --no-headers | awk '{print $1, $6}'
```

### Parse Logs

```bash
# Count errors per type from kubelet
journalctl -u kubelet --no-pager -o cat | awk '/error|Error/ {print}' | sort | uniq -c | sort -rn | head

# Extract timestamps of errors
journalctl -u kubelet --no-pager | awk '/error/ {print $1, $2, $3}' | tail -20
```

### Format Output

```bash
# Pretty table from kubectl
kubectl get pods --no-headers | awk '{printf "%-30s %-10s %-5s\n", $1, $3, $4}'

# CSV output
kubectl get pods --no-headers | awk '{print $1 "," $3 "," $4}'
```

### Conditional Processing

```bash
# Show pods using > 100m CPU (from kubectl top)
kubectl top pods --no-headers | awk '{
  cpu=$2; 
  gsub(/m/,"",cpu); 
  if (cpu+0 > 100) print $1, $2
}'
```

---

## 🎯 CKA Exam Patterns

| Scenario | awk Command |
|----------|-------------|
| Get pod names | `kubectl get pods --no-headers \| awk '{print $1}'` |
| Find non-running pods | `kubectl get pods --no-headers \| awk '$3!="Running"{print $1,$3}'` |
| Find NotReady nodes | `kubectl get nodes --no-headers \| awk '$2!="Ready"{print $1}'` |
| Extract IPs | `kubectl get pods -o wide --no-headers \| awk '{print $1,$6}'` |
| Count items | `kubectl get pods --no-headers \| awk 'END{print NR}'` |
| Skip header row | `... \| awk 'NR>1{print $1}'` |

---

## 💡 Pro Tips

1. **`--no-headers`** with kubectl → cleaner awk processing (no header row to skip)
2. **`$NF`** = last column → useful when columns vary
3. **`NR>1`** → skip headers when `--no-headers` isn't available
4. **awk vs jsonpath**: for simple column extraction, `awk` is FASTER to type than `-o jsonpath`
5. **Pipe chain**: `kubectl get ... | awk ... | sort | head` — very powerful combo
6. **`-F` for custom separators**: essential for parsing config files with `:` or `=` delimiters

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->