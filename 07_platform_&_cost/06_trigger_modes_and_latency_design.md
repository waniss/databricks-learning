

# 📄 06_trigger_modes_and_latency_design.md

# Trigger Modes, Latency & Cost Design

---

## 1️⃣ Why Trigger Mode Matters

Trigger mode determines:

* Latency
* Cluster runtime
* State lifecycle
* Cost model

Choosing wrong trigger:

* Wastes DBUs
* Causes instability
* Increases memory pressure

---

## 2️⃣ Trigger.AvailableNow (Incremental Batch)

Behavior:

* Process all currently available data.
* Stop automatically.
* Split into multiple micro-batches internally.

Best for:

* Scheduled ingestion
* Bronze pipelines
* Cost efficiency
* Large backfills

Cluster runs temporarily.

Ideal for job clusters.

---

## 3️⃣ Trigger.ProcessingTime

Example:

```python
.trigger(processingTime="10 seconds")
```

Behavior:

* Start micro-batch.
* Wait until 10-second window passes.
* Start next batch.

If set to:

```python
processingTime="0 seconds"
```

Spark starts next batch immediately.

Lowest latency in micro-batch model.

Used for:

* Near real-time dashboards
* Low-latency reporting

---

## 4️⃣ Continuous Trigger (Advanced)

Continuous mode:

* Lower latency
* Different execution model
* Not same as micro-batch

Rarely required for typical analytics.

Professional exam usually focuses on:

AvailableNow vs ProcessingTime.

---

## 5️⃣ Latency vs Cost Tradeoff

AvailableNow:

* Higher latency
* Low cost
* Short cluster lifetime

ProcessingTime:

* Lower latency
* Always-on cluster
* Higher cost

Architect must balance:

Business requirement vs budget.

---

## 6️⃣ State Store & Trigger Interaction

In continuous streaming:

* State remains in memory.
* Memory pressure accumulates.
* Watermarks critical.

In incremental batch:

* State destroyed after run.
* Memory reset each execution.

Important nuance for exam scenarios.

---

## 7️⃣ Professional Scenario Example

Scenario:

Pipeline runs every 6 hours.
Must process only new files.
Must minimize cost.

Correct solution:

Auto Loader + Trigger.AvailableNow + Job Cluster.

Another scenario:

Dashboard must update within 30 seconds.

Correct solution:

ProcessingTime trigger + Always-on cluster.

---

## 🔟 Professional Exam Traps

* AvailableNow is preferred over Once.
* ProcessingTime defines latency window.
* 0 seconds = immediate next batch.
* AvailableNow best for scheduled incremental ingestion.
* Trigger selection impacts cost.
* State store persists only in continuous runs.

---

## 🧠 Deep Understanding Check

1. Why is AvailableNow cost-efficient?
2. Why can Trigger.Once cause OOM?
3. Why does ProcessingTime increase steady cost?
4. Why does trigger affect state lifecycle?
5. Why must latency requirements drive trigger choice?
