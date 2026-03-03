

# 📄 01_lakeflow_jobs_and_workflows.md

# Lakeflow Jobs & Workflows (DAG Orchestration)

---

## 1️⃣ What Is Lakeflow Jobs?

Lakeflow Jobs (Workflows) are:

> The orchestration layer of Databricks.

They allow you to build:

* Directed Acyclic Graphs (DAGs)
* Multi-task pipelines
* Dependent execution flows

They are the “glue” connecting:

* Notebooks
* Python files
* SQL queries
* DLT pipelines
* dbt projects

---

## 2️⃣ Task Types

A single job can run:

* Notebook task
* Python script
* SQL task
* DLT pipeline
* dbt task

Professional nuance:
Understanding task composition is important for troubleshooting.

---

## 3️⃣ Repair & Run (Very Important)

Scenario:

Job has 10 tasks.
Task #8 fails.

Instead of rerunning entire pipeline:

You use:

> Repair & Run

Databricks will:

* Re-run failed task
* Re-run dependent downstream tasks
* Skip successful upstream tasks

This saves:

* Time
* DBUs
* Operational risk

Exam loves this feature.

---

## 4️⃣ Concurrency & Queueing

From your document :

* Default concurrency = 1
* Jobs can queue up to 48 hours
* Only one owner allowed
* Ownership cannot be assigned to group

Important governance nuance.

---

## 5️⃣ Job Triggers

Lakeflow Jobs support:

1️⃣ Scheduled (CRON)
2️⃣ File Arrival
3️⃣ Continuous

---

### File Arrival Trigger

* Automatically runs when file lands.
* Replaces need for Lambda-like systems.
* Common with Auto Loader.

Professional nuance:
Good ingestion design often combines Auto Loader + File Arrival.

---

## 6️⃣ Parameters Between Tasks

Jobs can:

* Pass parameters between tasks.
* Use outputs from Task A in Task B.

Example:

Task A computes processing date.
Task B receives it as parameter.

Important for incremental pipelines.

---

## 7️⃣ Cost Implications

Best practice:

* Use Job Clusters.
* Terminate after run.
* Avoid long-lived interactive clusters.

Orchestration design directly impacts cost.

---

## 🔟 Professional Exam Traps

* Repair & Run saves cost.
* Default concurrency is 1.
* Only one job owner allowed.
* File Arrival trigger exists.
* Queueing limit is 48h.
* Job clusters preferred for production.
* Jobs orchestrate DLT pipelines.

---

## 🧠 Deep Understanding Check

1. Why is Repair & Run cost-efficient?
2. Why is default concurrency important?
3. Why prefer Job Clusters?
4. When should File Arrival trigger be used?
5. Why can ownership not be group-assigned?



