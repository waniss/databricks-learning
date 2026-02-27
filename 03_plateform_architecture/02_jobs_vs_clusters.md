
---

## 🗓 WEEK 3 — DAY 3 — Clusters, Pools, Autoscaling & Production Isolation

---

## 1️⃣ All-Purpose (Interactive) vs Job Cluster

### All-Purpose Cluster

* Long-running
* Shared by multiple users
* Often manually started
* Higher DBU cost

Best for:

* Exploration
* Development
* Notebook debugging

Not ideal for production jobs.

---

### Job Cluster

* Created per job run
* Terminates automatically
* Isolated execution
* More cost-efficient for batch pipelines

Professional rule:

Production pipelines → Prefer job clusters.

---

## 2️⃣ Cluster Pools (Very Exam-Relevant)

Cluster pools reduce startup time.

Instead of provisioning VMs every time:

* Nodes are pre-warmed
* Job clusters reuse pool nodes
* Faster startup
* Lower latency

Important nuance:

Pools reduce startup latency,
NOT runtime cost.

Exam trap.

---

## 3️⃣ Autoscaling Nuance

Autoscaling adjusts number of workers based on workload.

Benefits:

* Adapts to variable workloads
* Avoids over-provisioning

Limitations:

* Scaling is reactive
* Does not fix skew
* Does not fix bad data layout
* Can introduce delay during scale-up

Professional insight:

Autoscaling helps elasticity, not bad architecture.

---

## 4️⃣ Instance Types Matter

CPU-optimized:

* Good for compute-heavy workloads
* Good for Photon

Memory-optimized:

* Good for shuffle-heavy
* Large aggregations
* Wide joins

Professional reasoning:

If shuffle spills to disk → memory issue.

Changing instance type may help more than adding nodes.

---

## 5️⃣ Spot vs On-Demand Instances

Spot:

* Cheaper
* Can be terminated
* Good for non-critical batch

On-demand:

* Stable
* More expensive
* Required for production-critical streaming

Exam scenario:

Mission-critical streaming job → avoid full spot cluster.

---

## 6️⃣ Isolation Strategy

Production architecture best practice:

* Separate dev, staging, prod
* Separate catalogs/schemas
* Separate job clusters

Avoid:

Single shared cluster for everything.

Professional exam loves governance + isolation thinking.

---

## 7️⃣ Concurrency Considerations

Interactive cluster:

* Shared resources
* Risk of contention
* Unpredictable performance

Job cluster:

* Dedicated resources
* Predictable runtime
* Better SLA management

---

## 8️⃣ Startup Overhead Tradeoff

Many small jobs:

* Each job cluster startup adds latency
* If tasks run for 30 seconds, startup overhead matters

Better strategy:

* Group related tasks
* Use multi-task workflow

Architectural tradeoff.

---

## 9️⃣ Professional Exam Traps

* Pools reduce startup time, not shuffle.
* Autoscaling does not fix skew.
* Spot instances risky for critical jobs.
* Interactive clusters not ideal for prod pipelines.
* Memory instance may outperform more CPU nodes for shuffle-heavy job.

---

## 🧠 Deep Understanding Check

Answer sharply:

1. Why are cluster pools used?
2. Why doesn’t autoscaling fix skew?
3. When would memory-optimized instances be preferred?
4. Why are spot instances risky for streaming?
5. Why are job clusters preferred for production?

---