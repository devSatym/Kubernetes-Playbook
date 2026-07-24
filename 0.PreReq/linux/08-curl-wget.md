> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🌐 curl & wget — Network Debugging

> **What they do:** Make HTTP requests from the command line. Test connectivity, endpoints, APIs.  
> **Why CKA cares:** Testing services, verifying ingress/networkpolicy, checking API server health.

---

## 🔥 curl Essential Options

| Flag | Meaning | Example |
|------|---------|---------|
| `-s` | Silent (no progress bar) | `curl -s http://svc:80` |
| `-o /dev/null` | Discard body | `curl -so /dev/null -w "%{http_code}" URL` |
| `-w "%{http_code}"` | Print HTTP status code | See above |
| `-k` | Skip TLS verification | `curl -k https://apiserver:6443` |
| `-I` | HEAD request (headers only) | `curl -I http://svc:80` |
| `-v` | Verbose (show full request/response) | `curl -v http://svc:80` |
| `-X <METHOD>` | HTTP method | `curl -X POST http://api/path` |
| `-H "key: val"` | Custom header | `curl -H "Authorization: Bearer $TOKEN"` |
| `-d 'data'` | POST data | `curl -d '{"key":"val"}' -X POST URL` |
| `--connect-timeout N` | Timeout for connection | `curl --connect-timeout 5 URL` |
| `-m N` | Max total time | `curl -m 10 URL` |
| `-L` | Follow redirects | `curl -L http://example.com` |
| `--cacert` | Specify CA cert | `curl --cacert /etc/kubernetes/pki/ca.crt` |
| `--cert` | Client cert | `curl --cert client.crt --key client.key` |

---

## ⚡ CKA Speed Combos

### Test Service Connectivity (from inside cluster)
```bash
# From a debug pod — test ClusterIP service
kubectl run tmp --image=busybox --rm -it --restart=Never -- wget -qO- http://svc-name.namespace:port

# Or with curl (if curlimages/curl available)
kubectl run tmp --image=curlimages/curl --rm -it --restart=Never -- curl -s http://svc-name.namespace:port

# Quick test with busybox nslookup
kubectl run tmp --image=busybox --rm -it --restart=Never -- nslookup svc-name.namespace
```

### Test from Node
```bash
# Test NodePort service
curl -s http://localhost:<nodeport>

# Test ClusterIP (from node, using the ClusterIP)
curl -s http://<cluster-ip>:<port>

# Test with timeout (don't hang if blocked by NetworkPolicy)
curl -s --connect-timeout 3 http://<ip>:<port>
```

### Test API Server Health
```bash
# Healthz endpoint
curl -k https://localhost:6443/healthz

# Livez endpoint
curl -k https://localhost:6443/livez

# Readyz endpoint  
curl -k https://localhost:6443/readyz

# With verbose (see cert errors)
curl -vk https://localhost:6443/healthz
```

### Test with Authentication
```bash
# Using service account token
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
curl -k -H "Authorization: Bearer $TOKEN" https://kubernetes.default.svc/api/v1/namespaces

# Using client cert
curl --cacert /etc/kubernetes/pki/ca.crt \
     --cert /etc/kubernetes/pki/apiserver-kubelet-client.crt \
     --key /etc/kubernetes/pki/apiserver-kubelet-client.key \
     https://localhost:6443/api/v1/nodes
```

### Check HTTP Status Code Only
```bash
# Just the status code (fastest check)
curl -so /dev/null -w "%{http_code}\n" http://svc:80
# Output: 200 (or 404, 503, etc.)
```

---

## 🔥 wget Essential (for busybox pods)

```bash
# busybox has wget but NOT curl, so:

# Simple GET
wget -qO- http://svc-name:port
# -q = quiet, -O- = output to stdout

# With timeout
wget -qO- --timeout=3 http://svc-name:port

# Just check if reachable (spider mode)
wget --spider -q http://svc-name:port && echo "OK" || echo "FAIL"
```

> 💡 In CKA, busybox pods only have `wget`, not `curl`. Remember `wget -qO-` syntax!

---

## 🎯 CKA Exam Patterns

| Scenario | Command |
|----------|---------|
| Test ClusterIP service | `k run tmp --image=busybox --rm -it --restart=Never -- wget -qO- http://svc.ns:port` |
| Test NodePort | `curl -s http://localhost:<nodeport>` |
| Verify NetworkPolicy | `curl --connect-timeout 3 http://<pod-ip>:<port>` |
| API server health | `curl -k https://localhost:6443/healthz` |
| Get HTTP status only | `curl -so /dev/null -w "%{http_code}" URL` |
| DNS resolution test | `k run tmp --image=busybox --rm -it --restart=Never -- nslookup svc.ns` |

---

## 💡 Pro Tips

1. **`--connect-timeout 3`** — ALWAYS use timeout when testing NetworkPolicy (don't hang forever)
2. **`wget -qO-`** — memorize this for busybox pods (no curl available)
3. **`-k` for API server** — skip cert verification during debugging
4. **`-so /dev/null -w "%{http_code}"`** — just get the status code, nothing else
5. **Use `kubectl run --rm -it`** — creates a temporary pod that auto-deletes after you exit
6. **DNS format:** `svc-name.namespace.svc.cluster.local` — full FQDN for cross-namespace access

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->