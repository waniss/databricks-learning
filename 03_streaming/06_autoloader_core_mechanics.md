

# 📄 06_autoloader_core_mechanics.md

# Auto Loader — Scalable File Ingestion

---

## 1️⃣ What Is Auto Loader?

Auto Loader is:

> A Databricks-native streaming source designed to ingest files from cloud storage at massive scale.

It uses:

```python
.format("cloudFiles")
```

It is not normal `spark.readStream`.

It is optimized for:

* Billions of files
* Massive directories
* Incremental ingestion
* Schema evolution

---

## 2️⃣ Why Auto Loader Exists

Standard Spark file streaming:

* Lists entire directory
* Becomes slow as file count grows
* Expensive in cloud API calls
* Metadata-heavy

Auto Loader solves:

* File discovery scalability
* Efficient incremental ingestion
* Lower cloud API cost

This is crucial for Bronze ingestion.

---

## 3️⃣ File Discovery Modes (Critical Distinction)

Auto Loader has two discovery modes:

### 1️⃣ Directory Listing (Default)

Spark lists files in directory.

Best for:

* Small to medium datasets (< ~1M files)

Limitations:

* Slows down as directory grows
* Expensive API calls

---

### 2️⃣ File Notification Mode

Uses cloud services:

* AWS SQS
* Azure Event Grid
* GCP Pub/Sub

Cloud service notifies Spark when file arrives.

Best for:

* Very high-volume ingestion
* Massive file directories
* Cost-sensitive workloads

Professional exam frequently tests:

Choosing correct mode based on scale.

---

## 4️⃣ Schema Inference

Auto Loader can:

* Infer schema automatically
* Sample data to detect columns

You must specify:

```python
.option("cloudFiles.schemaLocation", "path")
```

SchemaLocation stores inferred schema.

Without this:
Schema evolution will fail.

---

## 5️⃣ Schema Evolution

Auto Loader supports automatic schema evolution.

If new column appears:

* Pipeline does not crash.
* Delta table updated.
* Schema evolves.

Critical for JSON ingestion pipelines.

Professional nuance:
Schema evolution must be controlled in prod.

---

## 6️⃣ The Rescued Data Column

Problem:

If a column expected INT but receives STRING.

Instead of failing:

Spark places problematic fields in:

```text
_rescued_data
```

Benefits:

* Zero data loss
* Partial ingestion allowed
* Data quality fix downstream

Professional exam loves this concept.

---

## 7️⃣ Incremental Batch Pattern (Very Important)

Even though Auto Loader is streaming,
you can use it for batch-style execution.

Using:

```python
.trigger(availableNow=True)
```

Cluster:

* Starts
* Processes all available files
* Terminates

This is called:

Incremental batch pattern.

Ideal for:

* Scheduled ingestion every X hours
* Cost-efficient pipelines
* Bronze layer refresh

---

## 8️⃣ Why Trigger.AvailableNow Is Preferred

Your document  highlights:

Trigger.Once:

* Pulls all data in single massive batch
* Risk of OOM
* Risk of spill

Trigger.AvailableNow:

* Splits into manageable micro-batches
* More stable
* Rate-limit aware
* Professional choice

Exam nuance:
AvailableNow is modern, preferred approach.

---

## 9️⃣ Cost & Architecture Considerations

Auto Loader + Job Cluster:

* Start cluster
* Process new files
* Shut down cluster

Low idle cost.

Auto Loader + Continuous Trigger:

* Always-on cluster
* Low latency
* Higher steady-state cost

Tradeoff:
Latency vs Cost.

---

## 🔟 Professional Exam Traps

* cloudFiles required for Auto Loader.
* schemaLocation mandatory for schema evolution.
* File Notification for large directories.
* Rescued data prevents job failure.
* AvailableNow preferred over Once.
* Auto Loader scales to billions of files.
* Auto Loader reduces cloud listing cost.

---

## 🧠 Deep Understanding Check

1. Why is Auto Loader superior to standard file streaming?
2. When should File Notification mode be used?
3. Why is schemaLocation mandatory?
4. Why is AvailableNow safer than Once?
5. Why does Rescued Data improve reliability?


