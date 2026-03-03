
---

# 📄 03_data_quality_expectations.md

# Data Quality Expectations in Lakeflow

---

## 1️⃣ What Are Expectations?

Expectations enforce:

> Data quality rules inside Lakeflow pipelines.

Defined using:

```sql
EXPECT (condition)
```

Or Python equivalents.

---

## 2️⃣ Three Types of Expectation Behavior

From your document :

---

### 1️⃣ Retain (Track Only)

```sql
EXPECT (condition)
```

Behavior:

* Record kept
* Failure logged
* Pipeline continues

Used for monitoring.

---

### 2️⃣ Drop

```sql
EXPECT (condition) ON VIOLATION DROP ROW
```

Behavior:

* Invalid record removed
* Not written to target

Used for Bronze → Silver cleaning.

---

### 3️⃣ Fail

```sql
EXPECT (condition) ON VIOLATION FAIL UPDATE
```

Behavior:

* Pipeline stops immediately
* No further data processed

Used for strict governance.

---

## 3️⃣ Why Expectations Matter

Benefits:

* Data validation at ingestion
* Prevents dirty Silver/Gold layers
* Transparent failure logging
* Built-in quality monitoring

---

## 4️⃣ Interaction with CDC

Best practice:

Apply Expectations before APPLY CHANGES.

Ensures:

* Change stream is clean.
* No corrupt CDC events.
* Deterministic merge.

---

## 5️⃣ Performance Implications

Expectations:

* Add validation overhead
* Improve reliability
* Reduce downstream failures

Tradeoff:
Minor compute overhead vs major data corruption risk.

---

## 🔟 Professional Exam Traps

* Retain logs but keeps record.
* Drop filters record.
* Fail stops pipeline.
* Expectations applied before APPLY CHANGES.
* Expectations part of declarative Lakeflow model.
* Expectations do not automatically fix data.

---

## 🧠 Deep Understanding Check

1. When should you use FAIL instead of DROP?
2. Why apply expectations before CDC?
3. Why is Retain useful?
4. Why is Drop common in Bronze → Silver?
5. Why are Expectations declarative?
