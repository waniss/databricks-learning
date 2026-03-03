
---

# 📄 01_clusters_and_pools.md

# Clusters, Job Clusters & Pools

---

## 1️⃣ Cluster Types

Two main cluster types:

1️⃣ All-Purpose (Interactive)
2️⃣ Job Cluster

All-Purpose:

* Long-running
* Shared
* Used for development

Job Cluster:

* Created per job run
* Terminates automatically
* Isolated execution

Professional rule:

Production pipelines → Prefer job clusters.

---

## 2️⃣ Why Job Clusters Are Safer

Job clusters provide:

* Isolation
* Predictable resource usage
* Automatic termination
* Lower idle cost

Interactive clusters risk:

* Resource contention
* Idle DBU waste
* Accidental misuse

---

## 3️⃣ Cluster Pools

Cluster pools:

> Pre-provisioned nodes reused by job clusters.

Benefits:

* Reduced startup latency
* Faster job initialization
* Improved CI/CD pipelines

Important nuance:

Pools reduce startup time,
not runtime cost.

---

## 4️⃣ Autoscaling Behavior

Autoscaling:

* Increases workers when workload rises
* Decreases when workload drops

Limitations:

* Reactive, not predictive
* Does not fix skew
* Does not fix bad data layout

Autoscaling ≠ architecture optimization.

---

## 5️⃣ Instance Type Selection

CPU-optimized:

* Good for compute-heavy workloads

Memory-optimized:

* Better for shuffle-heavy workloads

Storage-optimized:

* Helpful for heavy spill scenarios

Instance type impacts cost more than worker count.

---

## 🔟 Professional Exam Traps

* Job clusters preferred for production.
* Pools reduce startup time only.
* Autoscaling does not fix skew.
* Interactive clusters waste DBUs if idle.
* Instance type choice impacts performance.

---

## 🧠 Deep Understanding Check

1. Why are job clusters preferred for production?
2. Why do pools not reduce runtime cost?
3. Why does autoscaling not fix skew?
4. When should memory-optimized instances be used?
5. Why are interactive clusters risky for pipelines?

---