> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->
# Kubernetes Playbook

> Learn Kubernetes by understanding **how it works**, **how it breaks**, and **how to fix it**.

This repository contains my personal Kubernetes knowledge base built while preparing for the **Certified Kubernetes Administrator (CKA)** and **Certified Kubernetes Security Specialist (CKS)** exams.

Although the notes started as certification preparation, they've evolved into a structured Kubernetes reference focused on real-world engineering and troubleshooting.

---

# What's Inside

Unlike traditional notes that only explain concepts, every topic follows a consistent learning framework.

Each topic covers:

- Concept Foundation
- Internal Working
- Architecture
- Component Interactions
- Dependencies
- YAML / Dockerfile Breakdown
- Critical Fields
- Common Misconfigurations
- Failure Patterns
- Troubleshooting Workflow
- Hands-on Practice
- Fast Debugging Strategy
- Security Considerations
- Best Practices
- Exam Insights
- Quick Revision Cards

---

# Topics Covered

## Kubernetes Core

- Architecture
- API Server
- ETCD
- Scheduler
- Controller Manager
- Kubelet
- Pods
- ReplicaSets
- Deployments
- DaemonSets
- StatefulSets
- Jobs
- CronJobs

---

## Networking

- Services
- DNS
- CoreDNS
- Ingress
- Network Policies
- CNI

---

## Storage

- Persistent Volumes
- PVC
- StorageClass
- CSI
- Volume Types

---

## Scheduling

- Taints
- Tolerations
- Affinity
- Node Selectors
- Resource Management

---

## Security (CKS)

- Pod Security
- RBAC
- Runtime Security
- Supply Chain Security
- Image Security
- Distroless Images
- Multi-stage Builds
- Admission Controllers
- AppArmor
- Seccomp
- Capabilities
- Falco
- Kyverno

---

## Troubleshooting

Real troubleshooting scenarios including:

- CrashLoopBackOff
- ImagePullBackOff
- Pending Pods
- Scheduling Failures
- DNS Issues
- Networking Problems
- Storage Issues
- RBAC Errors
- Control Plane Issues
- Worker Node Issues

---

# Designed For

- Kubernetes Beginners
- CKA Candidates
- CKS Candidates
- CKAD Candidates
- DevOps Engineers
- Platform Engineers
- Site Reliability Engineers (SRE)
- Cloud Engineers

---

# Philosophy

The goal isn't to memorize commands.

The goal is to understand:

- Why Kubernetes behaves the way it does.
- What happens when something breaks.
- How to identify the root cause quickly.
- How different components interact with each other.
- How to troubleshoot confidently under pressure.

---

# Repository Structure

```
cka/
    architecture/
    workloads/
    scheduling/
    networking/
    storage/
    troubleshooting/

cks/
    cluster-hardening/
    system-hardening/
    runtime-security/
    supply-chain-security/
    network-security/

labs/
    killerkoda/
    kodekloud/
    troubleshooting/

assets/
```

---

# Work in Progress

I'm continuously improving these notes while:

- Revising Kubernetes concepts
- Solving KillerKoda scenarios
- Practicing troubleshooting
- Preparing for CKA & CKS
- Exploring real-world production scenarios

Expect regular updates as I continue learning.

---

# Contributions

Found an issue?

Have a better explanation?

Want to add another troubleshooting scenario?

Feel free to open an Issue or submit a Pull Request.

---

If these notes help you in your Kubernetes journey, consider giving the repository a ⭐.

> **Original repo:** https://github.com/devSatym/Kubernetes-Playbook
> **Creator:** devSatym

<!-- 
Original repo: https://github.com/devSatym/Kubernetes-Playbook
Creator: devSatym
-->