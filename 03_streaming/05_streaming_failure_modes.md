
---

# 📄 05_streaming_failure_modes.md

# Streaming Failure Diagnosis & Root Causes

---

## 1️⃣ Memory Growth Over Time

Symptoms:

* Memory increases
* GC pressure
* Spill to disk

Root cause:
No watermark or unbounded state.

---

## 2️⃣ Streaming Fails After VACUUM

Symptoms:

* File not found error
* Snapshot reconstruction failure

Root cause:
Retention too aggressive.

---

## 3️⃣ Duplicate Writes in Sink

Symptoms:

* Repeated rows in target

Root cause:
Non-idempotent sink or checkpoint deletion.

---

## 4️⃣ Stage Stuck at 99%

Symptoms:

* One task runs long

Root cause:
Skew in streaming join.

---

## 5️⃣ Schema Change Crash

Symptoms:

* Query fails after schema change

Root cause:
State store incompatibility.

---

## 🔟 Professional Exam Traps

* Most streaming issues are state-related.
* VACUUM is common failure cause.
* Exactly-once is sink-level guarantee.
* Skew affects streaming too.
* Schema changes impact checkpoint.

---

## 🧠 Deep Understanding Check

1. Why does streaming memory grow?
2. Why can VACUUM break streaming?
3. Why can checkpoint deletion cause duplicates?
4. Why is skew dangerous in streaming?
5. Why are schema changes risky in streaming?

---
