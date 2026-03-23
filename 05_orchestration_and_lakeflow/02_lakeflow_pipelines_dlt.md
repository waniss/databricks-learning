
---

# 📄 02_lakeflow_pipelines_dlt.md

# Lakeflow Pipelines (DLT / LDP)

---

## 1️⃣ What Is Lakeflow Pipelines?

Lakeflow Pipelines (formerly DLT) is:

> A declarative pipeline framework for building streaming & batch data flows.

It abstracts:

* Checkpointing
* Retry logic
* Backfill
* Streaming writes
* Table materialization

You do NOT manually write `.writeStream`.

---

## 2️⃣ Three Object Types

From your document :

### 1️⃣ Streaming Tables (ST)

* Append-only ingestion
* Bronze layer
* Equivalent to readStream

---

### 2️⃣ Materialized Views (MV)

* Aggregations
* Complex transformations
* Silver/Gold layers
* Incremental or full refresh

---

### 3️⃣ Temporary Views

* Intermediate
* Not stored in catalog

---

## 3️⃣ SQL vs Python Syntax

SQL:

```sql
CREATE OR REFRESH STREAMING TABLE table_name AS
SELECT ...
```

Python:

```python
@dp.table
def table():
    return ...
```

Important exam nuance:
Recognize declarative keywords.

---

## 4️⃣ APPLY CHANGES Target Requirement

Important from your document:

> Target of APPLY CHANGES must be a Streaming Table.

But it behaves like a materialized table because it supports updates.

Professional nuance:
Standard streaming tables are append-only.
CDC target must support updates.

---

## 5️⃣ Automatic State Management

Lakeflow handles:

* Checkpoints
* Failure recovery
* Schema enforcement
* Backfills
* Retries

You do not manually configure checkpoint locations.

---

## 6️⃣ LDP vs DLT Naming

DLT = old name (notebook-based)
LDP = Lakeflow Pipelines (file-based)

Same core engine.

Exam may test naming nuance.

---

## 🔟 Professional Exam Traps

* Streaming Table ≠ Materialized View.
* APPLY CHANGES target must be streaming table.
* Declarative syntax required.
* Lakeflow manages checkpoints automatically.
* DLT and LDP refer to same system evolution.
* Do not manually use writeStream in LDP.

---

## 🧠 Deep Understanding Check

1. Why is Lakeflow declarative?
2. Why must APPLY CHANGES target be streaming table?
3. What is difference between ST and MV?
4. Why don’t you manually configure checkpoints?
5. Why is LDP more CI/CD friendly than notebook DLT?
