________________________________________
1. Modeling Data Management Solutions
1.1 Slowly Changing Dimensions
SCD is a Data Modeling pattern used to manage how the history of a record is stored in a dimension table when values change over time.
• SCD Type 1 (Overwrite): You simply overwrite the old value with the new one. There is no history.
Example: If a user changes their "Name," you just update the record.
• SCD Type 2 (History): You keep a history of every change by adding a new row with "version" or "start/end date" columns.
Example: If a user moves, you keep the old address row (marked as "inactive") and add a new row for the new address (marked as "active").
• The Hint: SCD is the Design. It’s the "How" you want your final table to look.
________________________________________
2. Data Ingestion
2.1 Auto Loader
2.1.1 Core Definition & Purpose
• What it is: A Databricks-native source used with spark.readStream to ingest files from cloud storage (S3, ADLS, GCS).
• The "Why": Standard Spark file streams become incredibly slow as the number of files in a directory grows. Auto Loader is designed to handle billions of files with high performance.
• Key Syntax: Uses the .format("cloudFiles") source.
________________________________________
2.1.2 The Two Discovery Modes
You must know which to choose based on file volume:
Directory Listing (Default)
Spark lists files in the input directory.
Best for:
Small to medium datasets (under 1 million files).
File Notification
Uses cloud services (AWS SQS, Azure Event Grid) to "notify" Spark when a new file arrives.
Best for:
Very high-volume directories where listing files takes too long or costs too much in cloud API fees.
________________________________________
2.1.3 Schema Inference & Evolution
This is a huge topic on the exam. Auto Loader makes your pipeline "smart."
• Schema Inference: You don't have to define columns; Spark scans a sample of data to "guess" the schema.
• Schema Evolution: If a new column appears in the source (e.g., a new field in a JSON file), Auto Loader can automatically update the target Delta table without crashing the job.
• The Key Option: cloudFiles.schemaLocation is required to store the inferred schema.
________________________________________
2.1.4 The Rescued Data Column
• The Problem: What happens if a file has a string in a column that is supposed to be an integer?
• The Solution: Instead of crashing, Spark puts the "bad" data into a hidden JSON column called _rescued_data.
• The Benefit: You can ingest the "good" parts of the record and fix the "bad" parts later. This ensures zero data loss.
________________________________________
2.1.5 Implementation for Batch (Incremental)
Even though it's called a "stream," you can use it for batch jobs.
Trigger.AvailableNow
Processes all currently available files and then shuts down the cluster.
The Note: This is the most cost-effective way to run incremental ETL on a schedule (e.g., once a day).
________________________________________
2.1.5.1 What is it?
Trigger.AvailableNow (introduced to replace the older Trigger.Once) tells Spark to:
• Check the source (S3/ADLS/Table) for all currently available new data.
• Process all that data in the most efficient micro-batches possible.
• Stop the query and shut down the cluster automatically once all "currently available" data is processed.
________________________________________
2.1.5.2 Why use it instead of Trigger.Once?
You might see both on the exam, but AvailableNow is the "Professional" choice.
Trigger.Once
Tries to pull all new data into a single, massive batch. If you have 5 million files, this often leads to Out of Memory (OOM) errors or "Spill to Disk."
Trigger.AvailableNow
Is rate-limit aware. It will split those 5 million files into multiple manageable micro-batches, processing them one after another in a single run. It’s much more stable.
________________________________________
2.1.5.3 The Professional Use Case: "Incremental Batch"
This is the most frequent exam scenario.
Scenario:
You have a job that needs to run every 6 hours. You want it to be "incremental" (only process new files) but you don't want a cluster running 24/7.
Solution:
Use Auto Loader + Trigger.AvailableNow + Databricks Jobs (Job Cluster).
Result:
The Job Cluster starts, Auto Loader finds the new files from the last 6 hours, processes them, and the cluster terminates. You only pay for the 10–15 minutes the job was running.
________________________________________
2.1.6 Trigger.ProcessingTime (The Micro-Batch Stream)
Trigger.ProcessingTime is the standard way to run a "continuous" stream. It sets a regular interval for the engine to check for new data.
• Mechanism: You provide a duration (e.g., 10 seconds). Spark will start a micro-batch, and once that batch finishes, it will wait until the 10-second window has passed before starting the next one.
• The "0 seconds" Case: If you set .trigger(processingTime='0 seconds'), Spark will start the next micro-batch the absolute instant the previous one finishes. This is the "fastest" way to run standard streaming.
• State Maintenance: Because the process is ongoing, the State Store (in memory) stays active. This is where watermarking and windowed counts live while waiting for the next trigger.
• Infrastructure Use: Usually used with Always-On Clusters (or Job Clusters set to "Continuous" mode).
• Professional Exam Note: It is the primary tool for "Latency Optimization." You use it when the business requires data to be refreshed in a dashboard within seconds or minutes of arriving.
________________________________________
3. Streaming State Management
3.1 Watermark
A watermark is a threshold that defines how long Spark should wait for late data based on the event time (the timestamp embedded in the data itself).
• Lateness Threshold: You define a duration (e.g., .withWatermark("timestamp", "10 minutes")).
• The Logic: Spark tracks the maximum event time seen so far. The "watermark" is calculated as:
Max Event Time − Lateness Threshold.
• Dropping State: Any data with an event time older than the current watermark is considered "too late" and is dropped. More importantly, Spark can now safely clear the old aggregation state from its memory for that time window.
________________________________________
The Scenario
You are a Data Engineer at a bank. You have a stream of transactions.
• The Business Rule: You need to calculate the total spend per user in 10-minute windows.
• The Reality: Transactions from mobile phones sometimes take time to upload. You decide to allow data to be up to 20 minutes late.
________________________________________
Phase 1: The Setup (Code)
Python
# We define a 20-minute watermark on the 'transaction_time' column
df = raw_transactions.withWatermark("transaction_time", "20 minutes") \
                     .groupBy(window("transaction_time", "10 minutes"), "user_id") \
                     .sum("amount")
________________________________________
Phase 2: The Timeline (The "Under the Hood" Logic)
Step 1: The Stream Starts (12:00 PM - 12:30 PM)
• Data arrives: Transactions with timestamps 12:05, 12:15, and 12:25 arrive.
• Max Event Time: 12:25 PM (the latest time Spark has seen in the data).
• Watermark Calculation: 12:25 PM minus 20 mins = 12:05 PM.
• State Store: Spark holds the sums for the 12:00-12:10, 12:10-12:20, and 12:20-12:30 windows in RAM.
________________________________________
Step 2: Late Data Arrives (at 12:31 PM)
New Record A: A transaction from 12:06 PM finally uploads.
Result: ACCEPTED. Because 12:06 is after the current watermark (12:05). Spark updates the sum for the 12:00-12:10 window in memory.
New Record B: A transaction from 12:02 PM finally uploads.
Result: DROPPED. Because 12:02 is before the watermark (12:05). Spark ignores this record forever to save resources.
________________________________________
Step 3: The Watermark Advances (at 12:45 PM)
New data arrives: A transaction with a timestamp of 12:45 PM arrives.
New Max Event Time: 12:45 PM.
New Watermark Calculation: 12:45 PM minus 20 mins = 12:25 PM.
________________________________________
Step 4: Cleanup (Eviction)
Spark looks at its RAM. It sees the window 12:00-12:10 and 12:10-12:20.
Since the Watermark is now 12:25 PM, Spark knows for a fact that no more "valid" data will ever arrive for those early windows.
Action: Spark deletes the 12:00-12:20 data from its RAM.
Output: If you are in Append Mode, this is the exact moment the final totals for those windows are written to your database.
________________________________________
4. Data Processing
4.1 CDC
CDC is the high-level process of identifying and capturing data that has changed in a source system (like a SQL database) so that those changes can be applied to your Lakehouse.
• How it works: It tracks INSERT, UPDATE, and DELETE events from the source.
• Professional Implementation: On Databricks, you use the APPLY CHANGES INTO API within Lakeflow Spark Declarative Pipelines to handle CDC.
• The Benefit: It automates the complex logic of "Upserting" (merging) data so you don't have to write manual MERGE statements.
• The Hint: CDC is the Strategy. It's the "What" you are trying to do.
create_auto_cdc_flow(
 target = "target_table",
 source = "cdc_source_table",
 keys = ["key_field"],
 sequence_by = col("operation_date"),
 apply_as_deletes = expr("operation_type = 'DELETE'"),
 except_column_list = ["operation_type", "operation_date"],
 stored_as_scd_type = 1
)
keys = ["key_field"]
This tells Spark which column defines the "Identity" of the row. When a change comes in, Spark looks for this key in the target table to decide whether to update an existing row or insert a new one.
sequence_by = col("operation_date")
This is your "Tie-Breaker." If two records for the same key_field arrive in the same batch, Spark compares their operation_date. The one with the latest date wins.
apply_as_deletes = expr("operation_type = 'DELETE'")
This is a boolean flag. If the logic inside that expression evaluates to True, Spark physically (or logically) removes that key from the target table.
except_column_list = ["operation_type", "operation_date"]
This is a "clean-up" step. It tells Spark: "Use these columns to process the change, but don't actually save them into the final target table." This keeps your Silver/Gold layers clean.
stored_as_scd_type = 1
This tells Spark to overwrite the existing data. You only care about the current state.
________________________________________
Can CDC work without CDF?
Yes. You can run the apply_changes (CDC) API against a standard Delta table that does not have CDF enabled.
How it works:
If CDF is off, Spark has to perform a full table scan or use the standard Delta log to find changes.
The Problem:
Without CDF, the "source" data you are reading must be a "pure" stream of changes (like a JSON file landing with an INSERT or DELETE flag).
The Limitation:
If the source table is updated or rows are deleted, and CDF is off, Spark has no easy way to "see" what the specific row-level change was. It only knows the file was modified.
Feature	CDC without CDF	CDC with CDF
Source Type	Append-only (New files)	Updates & Deletes (CDC)
Performance	Slows down as table grows	Stays fast (only reads changes)
SCD Type 2	Very difficult/Manual	Native and easy
Logic	Spark scans data files	Spark reads the Change Log
________________________________________
4.2 CDF
4.2.1 Core Definition & Purpose
What it is:
A Delta Lake feature that creates a row-level activity log for a table.
The "Why":
It allows you to see exactly what changed (the "delta") rather than just the final state. This is the foundation for efficient, incremental CDC (Change Data Capture).
Distinction:
Unlike Time Travel (which shows a snapshot of a table at a specific time), CDF shows the specific actions (Insert, Update, Delete) that occurred between versions.
________________________________________
4.2.2 The Four Change Types
When you query a CDF-enabled table, Spark adds a hidden column _change_type.
You must know these four values:
1.	insert — A brand new row
2.	delete — A row was removed
3.	update_preimage — The version of the row before it was changed
4.	update_postimage — The version of the row after it was changed
________________________________________
4.2.3 Implementation Checklist
Enablement
ALTER TABLE table_name
SET TBLPROPERTIES (delta.enableChangeDataFeed = true)
Reading Changes (Batch)
Must use:
.option("readChangeData", "true")
You must specify:
•	startingVersion
•	startingTimestamp
________________________________________
Reading Changes (Streaming)
Once the stream starts, the Checkpoint handles the versioning automatically.
________________________________________
4.2.4 Professional "Under the Hood" Mechanics
Storage Overhead
CDF writes extra files in a _change_data folder inside your table directory.
Only enable it on tables that serve as sources for downstream jobs.
________________________________________
Vacuum Interaction
VACUUM deletes CDF logs after the retention period (default 7 days).
If your downstream job does not run within that window, it will fail because the source changes are gone.
________________________________________
Efficiency
Using CDF + APPLY CHANGES INTO is the most performant way to handle updates and deletes because it eliminates the need for expensive full-table scans.
________________________________________
5. Performance
5.1 Data File Layout Optimization
5.1.1 Partitioning
Partitioning physically organizes data into folders based on a specific column.
Example
/year=2024/month=02/
How it works
Spark skips entire folders that don’t match your WHERE clause.
This is called Partition Pruning.
________________________________________
Best for
High-cardinality columns with known access patterns (date, region).
________________________________________
Traps
Over-partitioning
Partitioning by high-cardinality fields like user_id creates thousands of folders and slows metadata operations.
Data Skew
If one region contains most of the data, that partition becomes a bottleneck.
Rigidity
Changing partition columns requires a full table rewrite.
________________________________________
5.1.2 Z-Order Indexing
Z-Ordering is a technique used to co-locate related data inside files.
How it works
It maps multi-dimensional data into a one-dimensional Z-curve.
This keeps related values close together inside Parquet files.
________________________________________
Advantage
Unlike partitioning, it does not create folders.
It only reorganizes rows within files to improve data skipping.
________________________________________
Professional Rule
Always Z-Order on columns used in:
•	JOIN keys
•	WHERE filters
Example:
OPTIMIZE table_name ZORDER BY (customer_id)
________________________________________
5.1.3 Liquid Clustering
Liquid Clustering is the successor to both Partitioning and ZORDER.
It is now the default for new Delta tables.
________________________________________
How it works
Uses coordinate-based clustering instead of rigid folder structures.
________________________________________
Advantages
Incremental clustering
No folder explosion
Flexible clustering keys
You can change keys without rewriting data.
________________________________________
5.1.4 OPTIMIZE
OPTIMIZE performs file compaction.
________________________________________
Problem
Streaming and frequent batch writes create many small files.
This is called the Small File Problem.
________________________________________
Solution
OPTIMIZE merges these files into larger files (~1GB).
Benefits:
•	fewer files
•	faster query planning
•	lower metadata overhead
________________________________________
5.1.5 VACUUM
VACUUM is Delta Lake’s garbage collection command.
It deletes files that are no longer referenced by a table.
________________________________________
Default retention
7 days (168 hours)
________________________________________
Professional Exam Rules
Point of No Return
After VACUUM, time travel older than the retention period becomes impossible.
________________________________________
Safety Mechanism
Retention cannot be set to 0 hours unless:
spark.databricks.delta.retentionDurationCheck.enabled = false
________________________________________
Dry Run
Always run:
VACUUM table_name RETAIN X HOURS DRY RUN
first.
________________________________________
5.1.6 Auto Optimize
Auto Optimize includes two features.
________________________________________
Optimized Writes
Spark redistributes data to fewer executors before writing.
Result:
•	fewer files
•	larger files (~128MB)
________________________________________
Auto Compaction
After a write, Spark may automatically compact recently written files.
________________________________________
Best Practices
Bronze / Silver → Auto Optimize
Gold → Manual OPTIMIZE + ZORDER
________________________________________
5.1.7 Deletion Vectors
Legacy Method — Copy-on-Write
To delete a single row from a 1GB file:
1.	Spark reads the full file
2.	Removes the row
3.	Rewrites the file
This causes heavy I/O.
________________________________________
New Method — Deletion Vectors
Instead of rewriting files:
Delta writes a small bitmap file indicating which rows are deleted.
Example
"In file A, ignore row 500"
________________________________________
Benefits
Much faster DELETE / UPDATE / MERGE operations.
________________________________________
Perfect. I will continue exactly where the previous message stopped, and I will not modify a single sentence of your content.
Only structure and headings are improved.
Below is the continuation of your document.
________________________________________
6. Data Orchestration
6.1 Databricks Lakeflow Jobs
Workflows are the "Glue" of the platform. They allow you to build a DAG (Directed Acyclic Graph) to manage tasks.
Task Types
A single Workflow can trigger:
•	Notebooks
•	Python files
•	SQL queries
•	dbt projects
•	Delta Live Tables (DLT) pipelines
________________________________________
Key Features for the Exam
Repair and Run
If a Job with 10 tasks fails at task #8, you can fix the code and "Repair" it.
Databricks will only run the failed task and the ones following it, saving time and cost.
________________________________________
Parameters
You can pass arguments between tasks.
Example:
Task A calculates a date and passes it to Task B.
________________________________________
Conditional Logic
You can define conditional execution logic.
Example:
•	If Task A fails → run Alert task
•	If Task A succeeds → run Task B
________________________________________
Triggers
Scheduled
Uses CRON expressions.
________________________________________
File Arrival
Triggers the job the moment a file hits S3 or ADLS.
This replaces external orchestration tools like AWS Lambda.
________________________________________
Continuous
Restarts the job immediately after it finishes.
________________________________________
6.2 Delta Live Tables (DLT) / Lakeflow Pipelines (LDP)
The Three Object Types
Streaming Tables (ST)
Use for append-only ingestion (Bronze).
Handles incremental data.
Spark equivalent:
spark.readStream
________________________________________
Materialized Views (MV)
Used for complex transformations and aggregations (Gold).
Handles incremental refresh or full refresh.
Spark equivalent:
spark.read
but fully managed.
________________________________________
Temporary Views
Intermediate transformations only.
Not saved to the catalog.
________________________________________
SQL vs Python Syntax
The Professional Exam will test if you recognize the Declarative syntax.
SQL
CREATE OR REFRESH STREAMING TABLE name AS SELECT ...
or
CREATE OR REFRESH MATERIALIZED VIEW name AS SELECT ...
________________________________________
Python
Uses the pipelines module (aliased as dp).
@dp.table
→ Streaming Table
@dp.materialized_view
→ Materialized View
________________________________________
LDP vs DLT (Naming Nuance)
Lakeflow Pipelines (LDP)
•	Uses .py or .sql files
•	Module: pyspark.pipelines
________________________________________
DLT (Old)
•	Uses notebooks
•	Module: dlt
________________________________________
Shared Behavior
Both systems automatically manage:
•	Checkpoints
•	Retries
•	Backfill
You do not manually write:
.writeStream
or
checkpointLocation
________________________________________
6.3 Data Quality Expectations
Type	Syntax (SQL/Python)	Behavior
Retain	EXPECT(condition)	Record is kept but failure is logged
Drop	EXPECT(condition) ON VIOLATION DROP ROW	Record is removed
Fail	EXPECT(condition) ON VIOLATION FAIL UPDATE	Pipeline execution stops
________________________________________
6.4 AUTO CDC
CDC (Change Data Capture) systems typically provide a stream of changes with an operation flag.
Example operations:
•	INSERT
•	UPDATE
•	DELETE
Instead of writing complex MERGE INTO logic, Databricks allows you to define:
1.	The source of changes (stream)
2.	The primary key
3.	The ordering logic
________________________________________
SCD Modes
SCD Type 1 (Overwrite)
The target table keeps only the latest version.
________________________________________
SCD Type 2 (History)
The target table keeps every version.
Columns used:
•	__start_at
•	__end_at
________________________________________
Exam Tip
When using
stored_as_scd_type = 2
Databricks automatically manages:
•	__start_at
•	__end_at
•	__is_current
________________________________________
Exam Essentials
Sequence By
Mandatory.
Without it, an UPDATE could arrive before the INSERT.
sequence_by ensures the newest event wins.
________________________________________
Target Table
The target of APPLY CHANGES must be a Streaming Table.
________________________________________
Delete Handling
Deletes must be explicitly defined.
Example:
operation = 'D'
________________________________________
Data Quality
Expectations can be applied before apply_changes to validate incoming CDC streams.
________________________________________
Additional Notes
Queueing
Jobs can wait up to 48 hours if resources are unavailable.
________________________________________
Concurrency Limits
Default concurrency limit for jobs is 1.
________________________________________
Job Ownership
A Databricks Job must have exactly one owner.
Ownership cannot be assigned to a group.
________________________________________
Lakeflow Connect
New ingestion feature for SaaS sources such as:
•	Salesforce
•	SQL Server
________________________________________
7. Deployment and Testing
7.1 Databricks Asset Bundles (DAB)
What is a Bundle?
A bundle is a collection of files that defines your project.
It typically includes:
•	Source code (Python, Notebooks, SQL)
•	Workflow definitions
•	DLT pipelines
•	Compute configurations
•	Environment configuration
________________________________________
Key CLI Commands
The Databricks CLI is used to interact with bundles.
databricks bundle init
Creates a project structure.
________________________________________
databricks bundle validate
Checks YAML configuration without deploying.
________________________________________
databricks bundle deploy
Deploys resources to the workspace.
________________________________________
databricks bundle run <job-name>
Triggers a job remotely.
________________________________________
databricks bundle destroy
Removes deployed resources.
________________________________________
7.2 (Placeholder)
xxx
________________________________________
8. Data Governance & Sharing
8.1 Unity Catalog
Object Hierarchy
Unity Catalog introduces a three-level namespace.
catalog.schema.table
________________________________________
Metadata Layer
Metastore
Top-level container for metadata.
Typically created per region.
Can be attached to multiple workspaces.
________________________________________
Catalog
Used to separate:
•	environments
•	business units
Example:
prod_catalog
________________________________________
Schema
Equivalent to traditional databases.
Example:
sales_schema
________________________________________
Tables / Views / Volumes
Actual data objects.
________________________________________
Managed vs External Tables
Managed Tables
Databricks controls both metadata and storage.
If you run:
DROP TABLE
The data files are deleted.
________________________________________
External Tables
Databricks manages metadata only.
Files remain in your cloud storage.
________________________________________
Identities and Privileges
Unity Catalog uses centralized identity providers.
Examples:
•	Azure AD
•	Okta
________________________________________
Principals
Users
Individual accounts.
________________________________________
Groups
Best practice for permission management.
________________________________________
Service Principals
Identities used by automated pipelines.
________________________________________
GRANT Example
GRANT USAGE ON CATALOG main TO data_science_group
GRANT SELECT ON TABLE main.default.users TO data_science_group
________________________________________
Volumes
Volumes manage non-tabular data.
Examples:
•	PDFs
•	Images
•	ZIP files
________________________________________
Types
Managed Volumes
Stored in Unity Catalog managed storage.
________________________________________
External Volumes
Point to external cloud storage paths.
________________________________________
Advanced Security
Row Filters
Restrict rows based on conditions.
Example:
WHERE region = 'US'
________________________________________
Column Masking
Hide sensitive values.
Example:
Show only last 4 digits of a credit card.
________________________________________
Lineage and Auditing
Unity Catalog automatically records:
•	table lineage
•	column lineage
•	query access
This supports regulatory compliance such as:
•	GDPR
•	HIPAA
________________________________________
8.2 Dynamic Views
Dynamic Views use functions such as:
is_account_group_member()
or
current_user()
to dynamically filter or redact data.
________________________________________
Storage Credentials
External locations are secured using:
•	IAM roles
•	Service principals
This prevents hardcoding access keys.
________________________________________
8.3 Delta Sharing
Delta Sharing allows secure data sharing without data replication.
________________________________________
How It Works
1.	Recipient requests table access.
2.	Delta Sharing server verifies permissions.
3.	Server returns short-lived signed URLs.
4.	Recipient reads Parquet files directly from cloud storage.
________________________________________
Sharing Types
Databricks-to-Databricks (D2D)
Shares:
•	tables
•	views
•	volumes
•	notebooks
Supports:
•	time travel
•	streaming reads
•	CDF access
________________________________________
Databricks-to-Open (D2O)
Allows sharing with:
•	Pandas
•	Power BI
•	Spark
•	external systems
Uses:
•	bearer tokens
•	OIDC authentication
________________________________________
Costs & Roles
Admin
Only Metastore admins or users with CREATE SHARE privilege can create shares.
________________________________________
Egress
Cross-region sharing may incur cloud egress fees.
________________________________________
Constraint
Delta Sharing is read-only.
________________________________________
8.4 Lakehouse Federation
Lakehouse Federation allows querying external systems without ingestion.
________________________________________
Supported Sources
•	Snowflake
•	Amazon Redshift
•	Google BigQuery
•	MySQL
•	PostgreSQL
•	Oracle
•	Azure SQL
________________________________________
When to Use Federation
Federation is useful when:
•	live data is required
•	ingestion pipelines are expensive
•	API limits prevent frequent extraction
________________________________________
Limitations
Performance depends on:
•	network latency
•	source database performance
________________________________________
Comparison
Feature	Delta Sharing	Lakehouse Federation
Primary Goal	Share data with others	Query external databases
Data Format	Delta tables	MySQL, Postgres, Snowflake
Storage	Provider storage	External DB
Access	Read-only	Read-only
UC Integration	Shares & Recipients	Foreign Catalogs
Performance	High	Variable

