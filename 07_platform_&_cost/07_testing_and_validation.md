
# 📄 07_testing_and_validation.md

# Deployment & Testing Strategies

---

## 1️⃣ Why Testing Matters

In a Databricks project, deployment and CI/CD are only half the battle. You must verify that the code and data behave as expected before promoting to production. Testing helps catch logic bugs, schema mismatches, and performance regressions early.

---

## 2️⃣ Unit & Integration Tests

* Write Python tests using `pytest` or `unittest` against modular code (`.py` files).  
* Use `databricks-connect` or the `dbx` CLI to run tests locally with a lightweight Spark session.  
* For notebooks, convert critical logic to modules and test those instead of the notebook cells directly.  
* Integration tests can execute a small job on a dev workspace, verify outputs, and clean up resources.

---

## 3️⃣ Data Quality Validation

* Leverage `EXPECT` statements in Delta Live Tables or Lakeflow Pipelines to assert invariants (`non-null`, `range checks`, `fk relationships`).  
* Standalone SQL validation queries can be executed as part of a job; failures raise alerts.  
* Synthetic test datasets simplify edge‑case coverage.

---

## 4️⃣ CI/CD & Pre‑Deployment Checks

* Run `databricks bundle validate` in your CI pipeline to catch YAML/structure errors before deployment.  
* Include unit tests and data quality checks as pipeline steps (GitHub Actions, Azure DevOps, etc.).  
* Automate environment promotion: dev → staging → prod, running smoke tests at each stage.

---

## 5️⃣ Best Practices

* Keep tests fast and idempotent.  
* Isolate resource usage by using short‑lived job clusters.  
* Parameterize tests to run against different targets/environments.  
* Version control tests alongside code in the same repository.

---

## 🔟 Professional Exam Traps

* Databricks has no built-in test runner; you use standard Python/SQL tools.  
* `bundle validate` checks configuration but not functional correctness.  
* DLT expectations are a form of runtime data validation, not unit tests.  
* Tests should run in CI before `bundle deploy` to avoid costly rollbacks.

---

## 🧠 Deep Understanding Check

1. How do expectations differ from unit tests?  
2. Why is `bundle validate` part of CI?  
3. What makes a good integration test on Databricks?  
4. Why should you parameterize tests by target workspace?  
5. Why avoid testing notebooks directly?
