
---

# 📄 03_watermarks_and_late_data.md

# Watermarks & Late Data Handling

---

## 1️⃣ What Is a Watermark?

Watermark defines:

> How late data is allowed to arrive before being dropped.

Example:

```python
.withWatermark("event_time", "10 minutes")
```

Watermark time = max_event_time_seen − delay.

---

## 2️⃣ Why Watermarks Exist

Without watermark:

State grows indefinitely.

Watermarks allow:

* State eviction
* Window closure
* Memory control

Watermarks prevent unbounded state.

---

## 3️⃣ How Watermark Works

For each micro-batch:

1️⃣ Track max event_time seen.
2️⃣ Compute watermark threshold.
3️⃣ Evict state older than watermark.

Watermark moves forward only.

Never backward.

---

## 4️⃣ Late Data Behavior

If data arrives later than watermark threshold:

It may be dropped.

This is correctness vs memory tradeoff.

Aggressive watermark:

* Lower memory
* Higher risk of data loss

Lenient watermark:

* Higher memory
* Better correctness

Architectural balance required.

---

## 5️⃣ Watermarks & Windows

Windows close only when watermark passes window end.

Without watermark:
Windows never close.
State persists forever.

---

## 🔟 Professional Exam Traps

* Watermark does not reorder data.
* Watermark is eviction mechanism.
* Late data beyond threshold may be dropped.
* Watermark affects memory footprint.
* No watermark → state growth.

---

## 🧠 Deep Understanding Check

1. Why does watermark depend on max event time?
2. Why is watermark a tradeoff?
3. Why does state grow without watermark?
4. Why can aggressive watermark cause data loss?
5. Why must windows rely on watermark?
