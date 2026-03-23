

# 📄 03_data_quality_expectations.md

# Data Quality Expectations

---

Type	Syntax (SQL/Python)	Behavior
Retain	EXPECT(condition)	Record is kept but failure is logged
Drop	EXPECT(condition) ON VIOLATION DROP ROW	Record is removed
Fail	EXPECT(condition) ON VIOLATION FAIL UPDATE	Pipeline execution stops
