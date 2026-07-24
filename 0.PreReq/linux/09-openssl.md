> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# 🔐 openssl — Certificate Inspection for CKA

> **What it does:** Swiss-army knife for TLS/SSL — inspect, verify, and debug certificates.  
> **Why CKA cares:** Kubernetes is cert-heavy. Expired certs = broken cluster. You MUST be able to check cert details.

---

## 🧠 Certificate Locations (Memorize!)

| Path | Certificate For |
|------|-----------------|
| `/etc/kubernetes/pki/ca.crt` | Cluster CA |
| `/etc/kubernetes/pki/ca.key` | Cluster CA key |
| `/etc/kubernetes/pki/apiserver.crt` | API Server |
| `/etc/kubernetes/pki/apiserver.key` | API Server key |
| `/etc/kubernetes/pki/apiserver-kubelet-client.crt` | API→kubelet auth |
| `/etc/kubernetes/pki/apiserver-etcd-client.crt` | API→etcd auth |
| `/etc/kubernetes/pki/etcd/ca.crt` | etcd CA |
| `/etc/kubernetes/pki/etcd/server.crt` | etcd server cert |
| `/etc/kubernetes/pki/front-proxy-ca.crt` | Front proxy CA |
| `/etc/kubernetes/pki/sa.pub` | Service account public key |

---

## 🔥 Essential openssl Commands

### Inspect a Certificate (MOST USED)

```bash
# Full certificate details
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout

# Just the subject (who is this cert for?)
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -subject -noout

# Just the issuer (who signed it?)
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -issuer -noout

# Expiration date
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -enddate -noout

# Start + End dates
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -dates -noout

# Subject Alternative Names (SANs) — what names/IPs are valid
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout | grep -A1 "Subject Alternative Name"
```

### Check Certificate Expiry

```bash
# Check if cert expires within 30 days
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -checkend 2592000 -noout
# Exit 0 = valid, Exit 1 = expiring/expired

# Human-readable expiry
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -enddate -noout
# Output: notAfter=Jan 15 12:00:00 2026 GMT
```

### Verify Certificate Chain

```bash
# Verify cert was signed by the CA
openssl verify -CAfile /etc/kubernetes/pki/ca.crt /etc/kubernetes/pki/apiserver.crt
# Output: OK or error

# Verify etcd cert against etcd CA
openssl verify -CAfile /etc/kubernetes/pki/etcd/ca.crt /etc/kubernetes/pki/etcd/server.crt
```

### Test TLS Connection

```bash
# Connect to API server and show cert
openssl s_client -connect localhost:6443 -showcerts </dev/null 2>/dev/null | head -20

# Connect to etcd
openssl s_client -connect localhost:2379 \
  -CAfile /etc/kubernetes/pki/etcd/ca.crt \
  -cert /etc/kubernetes/pki/etcd/server.crt \
  -key /etc/kubernetes/pki/etcd/server.key </dev/null
```

---

## ⚡ CKA Speed Combos

### Quick Cert Health Check (All Certs)
```bash
# Check expiry of all K8s certs in one go
for cert in /etc/kubernetes/pki/*.crt; do
  echo "=== $cert ==="
  openssl x509 -in "$cert" -enddate -subject -noout
  echo
done
```

### Check API Server SANs
```bash
# Critical: if API server cert doesn't have the right SANs, connections fail
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout | grep -A1 "Subject Alternative Name"
```

### Decode a Base64 Secret Cert
```bash
# If cert is stored in a K8s secret (base64 encoded)
kubectl get secret my-tls -o jsonpath='{.data.tls\.crt}' | base64 -d | openssl x509 -text -noout
```

### Check kubeadm Cert Expiry
```bash
# Easiest way (kubeadm does it for you)
kubeadm certs check-expiration

# Renew all certs
kubeadm certs renew all
```

---

## 🎯 CKA Exam Patterns

| Scenario | Command |
|----------|---------|
| Check cert expiry | `openssl x509 -in cert.crt -enddate -noout` |
| Check cert subject | `openssl x509 -in cert.crt -subject -noout` |
| Check SANs | `openssl x509 -in cert.crt -text -noout \| grep -A1 "Subject Alternative"` |
| Verify cert chain | `openssl verify -CAfile ca.crt cert.crt` |
| Full cert details | `openssl x509 -in cert.crt -text -noout` |
| Decode secret cert | `kubectl get secret ... \| base64 -d \| openssl x509 -text -noout` |
| All cert expiry | `kubeadm certs check-expiration` |

---

## 💡 Pro Tips

1. **`-text -noout`** — always use together: `-text` shows human-readable, `-noout` suppresses raw PEM
2. **`kubeadm certs check-expiration`** — faster than checking each cert manually
3. **SANs are critical** — if API server cert doesn't include the node IP/hostname, TLS fails
4. **etcd has its OWN CA** — `/etc/kubernetes/pki/etcd/ca.crt` (different from cluster CA!)
5. **`grep -A1 "Subject Alternative"`** — quick SAN extraction from verbose output
6. **Know the cert paths** — memorizing the paths saves huge time in troubleshooting

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->