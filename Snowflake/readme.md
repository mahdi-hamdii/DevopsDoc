SAS Product
pay as you use payment model
Automatic optimisation
Shared disk or shared nothing: 
    * Shared disk: keeping data in one single disk
    - Shared nothing: Hadoop and Spark
    - SnowFlake: Multi cluster shared data architecture
1. Storage Layer: 

* When data files are loaded or rows inserted into a table, snowflake reorganizes the data into its proprietary compressed, columnar table file format.
* The data is loaded or inserted is also partitioned into what snowflake call micro partitions
* Storage is billed by how much is stored based on a flat rate per TB calculated monthly.
* Data is not directly accessible in the underlying blob storage, only via SQL commands.

2. Query Processing Layer:

* Consists of Virtual warehouses that execute the processing tasks required to return results for most SQL Statements
* A virtual Warehouse is a named abstraction for a cluster of a cloud-based compute instances that snowflake manage.
* Underlying nodes of a virtual warehouse cooperate in a similar way to a shared-nothing compute clusters making use of local caching.
* Virtual warehouses come in multiple "t-shirt" sizes indicating their relative compute power.
* All running virtual warehouses have consistent access to the same data in the storage layer.

3. Services Layer:
* Shared between accounts
* Is a collection of f


A. Snowflake Editions:
    1. Standard:
    2. Enterprise
    3. Business Critical
    4. Virtual Private Snowflake
- **Search Optimization**: Not available for `Standard`
- **Materialized Views**: Not Available for `Standard`
- **Column and row access policies**: Not available for `Standard`
- **Multi Cluster Virtual Warehouses**: Not Available for `Standard`
- **Database Failover and failback**: Not available for `Standard`
- **Tri-Secret secure**: Not available for `Standard` and for `Enterprise`
- **Private connectivity**: Not Available for `Standard` and for `Enterprise`
- **Support For compliance regulation**: Not available for `standard` and for `Enterprise`
- **Dedicated Metastore and pool of compute resources**: Not available for `Standard` `Enterprise` and `Business Critical`
<br>

# B. Functions & Procedures:
- UDFs can be written in Java, Javascript, SQL and Python.
- Java UDFs cannot be designated as secure.
- Javascript UDFs can be recursive.
- UDF can only execute with the owner right
- External Functions are Slower, Less secure, Egress charges, Scalar only and not sharable.
- Stored Procedures can be written in Javascript, SQL, PYTHON, JAVA and Scala.
- Stored Procedures can execute with the owner's rights or the callers rights.
- Stored procedures can mix javascript and SQL in their definition using Snowflake's Javascript API.
- Stored procedures can call itself recursivly.
<br>

# C.Sequences:
![Sequences](./../assets/sequences.PNG)
- Sequences cannot guarantee their values will be gap free.
<br>

# D. Tasks & Streams:
- A task is an object used to schedule the execution of a SQL command or a stored procedure.
- Requires `ACCOUNTADMIN` role or `CREATE TASK` privilege.
- After a task is created we need to execute `ALTER TASK TASK_NAME RESUME` to start the task.
- we can create a Tree of tasks by adding the `AFTER TASK1` in the create statement.
![Tree of Tasks](./../assets/tree_of_task.PNG)
- A stream is an object created to view & track DML changes to a source table `Inserts`, `updates` & `deletes`.
- Stream adds three extra columns to the table `METADATA$ACTION`, `METADATA$ISUPDATE` and `METADATA$ROW_ID`.
![Stream](./../assets/stream.PNG)

<br>

# E. Billing Overview:
- There is two payment models: On-demand (pay for usage as you go) and capacity (pay for usage upfront).
- We will get billed for: `Virtual warehouse services`, `Cloud services`, `Serverless services`, `Storage` and `Data transfer`.
- Pay in **Credits**: `Virtual warehouse`, `Cloud services` and `Serverless services`
- Pay in **Dollars**: `Storage` and `Data transfer`.
- **Virtual Warehouse services**: Credit calculated per second based on size of VW with a minimulm of 60 seconds.
- **Cloud Services**: Credit calculated at a rate of 4.4 Credits per compute hour. Only Cloud services exceeding 10% of the daily usage of the compute resources are billed (This is called `Cloud Services Adjustment`) 
- **Serverless Services**: Serverless features are composed of compute services and cloud services but Cloud services Adjustment does not apply to Cloud services usage.
- **Database Storage**: Data storage is calculated monthly based on the average number of on-disk bytes per day in `Database tables` & `internal stages`.
- **Data Transfer**: Data transfer charges apply when moving data from one region to another or from one cloud platform to another, Unloading Data using `COPY INTO` command, External functions transferring data out and into snowflake.
![SnowCD](./../assets/snowcd.PNG)
- SnowCD is a connectivity and Diagnosis tool.

<br>

# F. Connectors and Drivers
![Connectors and Drivers](./../assets/connectors.PNG)
- Snowflake Partner tools:
    - **BI**: Tableau, PowerBI, Qlik, Thoughtspot
    - **Data Integration**: dbt Labs, Informatica, Pentaho, Fivetran
    - **Securiy & Governane**: Collibra, DATADOC, VAULT, Data.world
    - **Machine Learning & Data Science**: DataRobot, Dataiku, Amazon SageMaker, Zepl
    - **SQL Development & Management**: sqlDBM, SEEKWELL, ackolade, Agine data engine
- Snowflake partner connect is a feature to expedite connectivity with partnered tools.
<br>

# J. Snowflake Scripting:
- Snowflake scripting is an extension to Snowflake SQL that adds support for procedural logic.
![Snowflake Scripting](./../assets/scripting.PNG)
![Cursor](./../assets/cursor.PNG)
![ResultSet](./../assets/resultset.PNG)
- snowpark api is for Java, scala and Python.

<br>

# H.Access Control overview:
- `RBAC`: is an access control framework in which access privileges are assigned to roles and in turn assigned to users.
- Snowflake combines RBAC with `Discretionnary Access Control DAC` in which each object has an owner, who can in turn grant access to that object. 
![Securable Objects](./../assets/securable.PNG)
- **Role**: A role is an entity to which privileges on securable objects can be granted or revoked.
- **Roles**: A user can have multiple roles and switch between them within a snowflake session.
![System defined roles](./../assets/system_defined_roles.PNG)
- Privileges are managed using the `GRANT` and `REVOKE` commands.
- Future grants allow privileges to be defined for objects not yet created.
![privileges](./../assets/privileges.PNG)
- MFA in snowflake is powered by a service called `DUO Security`.
- MFA is enabled on a per-user basis & only via the UI.
- **MINS_TO_BYPASS_MFA**: specifies the number of minutes to temporarily disable MFA for the user so that they can log in.
```conf
ALTER USER user1 SET
MINS_TO_BYPASS_MFA=10
```
- **DISABLE_MFA**
- **ALLOWS_CLIENT_MFA_CACHING**: MFA token caching reduces the number of prompts that must be acknowledged while connecting and authenticating to snowflake.
- **Network Policies**: provide the user with the ability to allow or deny access to their snowflake account based on a list of IP @. Netpol can be applied to the account level or to individual users.
![netpol](./../assets/netpol.PNG)
- Blocked IP ranges are applied first. Netpol currently supports only IPV4 addresses.
- If a user is associated to both an account-level and user-level netpol, the user level netpol takes precedence.
- Only one netpol can be associated with an account or user at any one time by `SECURITYADMIN` or `ACCOUNTADMIN`.
- Snowflake follows a Hierarchical Key Model. The root key is based on AWS CloudHSM.
- **Key Rotation**: is the practice of transparently replacing existing account and table encryption keys every 30 days with a new key.
- **Re-Keying**: Once a retired key exceeds 1 year, Snowflake automatically creates a new encryption key and re-encrypts all data previously protected by the retired key using the new key.

<br>

## a.Column Level Security:
- **Dynamic Data Masking**: sensitive daat in plain text is loaded into snowflake and it is dynamically masked at the time of query for unauthorized users.
![Dynamic Data Masking](./../assets/dynamic_data_masking.PNG)
- **External Tokenization**: Tokenized data is loaded into snowflake, which is detokenized at query run-time for authorized users via masking policies that call an external tokenization service using external functions.
![External Tokenization](./../assets/external_tokenization.PNG)

## b. Row level Security
- **Row Access policies**: enable a security team to restrict which rows are returned in a query.
- Row access policies are evaluated before data masking policies.

## c. Secure views:
- Secure views are a type of view designed to limit access to the underlying tables or internal structural details of a view.
- The definition of a secure view is only available to the object owner.
- Secure views bypass query optimization which may inadvertently expose data in the underlying table.

# I. Account Usage and Information Schema:
![Account usage](./../assets/account_usage.PNG)
![Information Schema](./../assets/information_schema.PNG)
- Account usage includes dropped objects vs Information schema NO.
- Latency of data From 45 minutes to 3 hours vs Information schema none.
- Retention of historical data 1 year vs 7 days to 6 months.

# J. Virtual warehouse:
- Virtual warehouses execute DQL operations (Select), DML operations (update) and Data loading operations (COPY INTO).
- Virtual warehouses can be created via the snowflake UI or through SQL commands.
- Virtual warehouses can created in 10 t-shirt sizes from x-small to 6X-large (x-small, small, medium, large , X-large .. 6X-large)
- Data loading does not typically required large virtual warehouse and sizing up does not guarantee increased data loading performance.
![VWarehouse Billing](./../assets/warehouse_billing.PNG)
- Virtual warehouse state is either: `STARTED`, `SUSPENDED` or `RESIZING`. By default when a warehouse is created it is in the started state.
- **AUTO_SUSPEND=300**: Specifies the number of seconds of inactivity after which a warehouse is automatically suspended.
- **AUTO_RESUME=TRUE**: Specifies whether to automatically resume a warehouse when a SQL statemnt is submitted to it.
- **INITIALLY_SUSPEND=TRUE**: Specifies whether the warehouse created initially in the suspended state.
- Resizing a running warehouse does not impact running queries. This additional compute resources are used for queued and new queries.
- **Standard Scaling Policy**: When a query is queued a new warehouse will b eadded to the group immediately. Every minute a background process will check if the load on the least busy warehouse can be redistributed to another warehouse. If this condition is met after 2 consecutive minutes a warehouse will be marked for shutdown.
- **Economy Scaling Policy**: When a query is queued the system will estimate if there's enough query load to keep a new warehouse busy for 6 minutes. Every minute a background process will check if the load on the least busy warehouse can redistributed to another warehouse. If this condition is met after 6 consecutive minutes a warehouse will be marked for shutdown.
- **MAX_CONCURRENCY_LEVEL=6**: Specifies the number of conurrent SQL statements that can be executed against a warehouse before either it is queued or additional compute power is provided.
- **STATEMENT_QUEUED_TIMEOUT_IN_SECONDS=60**: Specifies the time, in seconds, a SQL statement can be queued on a warehouse before it is aborted.
- **STATEMENT_TIMEOUT_IN_SECONDS=600**: it specifies the time, in seconds after which any running SQL statement on a warehouse is aborted.
<br>

# K. Resource Monitors:
- Resource monitors are objects allowing users to set credit limits on user managed warehouses.
- Ressource monitors can be set either on the account or individual warehouse level. Limits can be set for a specified interval or data range.
- When limits are reached an action can be triggered such as notify user or suspend warehouse.
- Ressource monitor can only be created by account administrators.

# L. Performance and Tuning Overview:
![Query Performance Analysis Tools](./../assets/query_history.PNG)
![DB Order of Execution](./../assets/execution_order.PNG)
# M. Caching:
- **Metadata Cache**: Snowflake has a high availability metadata store which maintains metadata object informations and statistics. Some queries can be completed purely using metadata, not requiring a running virtual warehouse.
![Metadata Cache](./../assets/query_caching.PNG)
- **Result Cache**: New query exactly matches previous query. The underlying table data has not changed. The same role is used as the previous query.
- Result cache can be disabled using the session parameter `USE_CACHED_RESULT`.
- **Warehouse Cache**: Virtual warehouses have local SSD storage which maintains raw table data used for processing a query. The larger the virtual warehouse the greater the local cache. It can be used partially, retrieving the rest of the data required for a query from remote storage.
- `Materialized Views`: is pre-computed & persistent data set derived from a SELECT query.
- Snowflake maintains the following clustering metadata for micro-partitions in a table: `Total number of micro partitions`, `Number of overlapping micro-partitions`, `Depth of Overlapping Micro-partitions`
- **SYSTEM$CLUSTERING_INFORMATION**
- Snowflake supports specifying one or more table columns/expressions as a clustering key for a table.
- Clustering aims to co-locate data of the clustering key in the same micro partitions.
- Clustering should be reserved for large tables in the multi-terabyte range.
- Clusering improves performance of queries like: `WHERE`, `JOIN`, `ORDER BY` and `GROUP BY`.
- Snowflake recommend a maximum of 3 or 4 columns or expressions per key.
- As DML operations are performed on a clustered table, the data in the table might become less clustered.
- Initial clustering and subsequent reclustering operations consume `compute & storage credits`.
- Clustering is recommended for large tables which do not frequently change and are frequently queried.

# Search Optimization Service:
- Search optimization service is a table level propery aimed at improving the performance of selective point lookup queries.
- The search optimization service is an enterprise edition feature.
- The keyword `OVERWRITE` will truncate a table before new values are inserted into it.
- `Stages` are temporary storage locations for data files used in the data loading and unloading process.
![Internal Stages](./../assets/internal_stages.PNG)
- `External stages`: reference data files stored in a location outside snowflake.
![Stage helper commands](./../assets/stage_helper_commands.PNG)
- The `PUT` command uploads data files from a local directory on a client machine to any of the three internal stages. Duplicate files uploaded to a stage via PUT are ignored. 
- Uploaded files are automatically encrypted with a `128-bit key` with optiona support for `256-bit key`.
- **COPY INTO**: statement copies the content of an internal or external stage or external location directly into a table.
- The following formats can be uploaded into snowflake: `Delimited files (CSV, TSC, etc)`, `JSON`, `AVRO`,`ORC`,`PARQUET`,`XML`.
- The copy into command requires a user created virtual warehouse to execute and the Load history is stored in the metadata of the target table for 64 days, which ensures files are not loaded twice.
- **LOAD Transformatons** allows users to perform: `Column reodering`, `Column omission`, `casting` and `Truncate test string that exceed target length`.
![COPY INTO Options](./../assets/copy_into_options.PNG)
- **VALIDATION_MODE**: Optional parameter allows you to perform a dry-run of load process to expose errors (RETURN_N_ROWS, RETURN_ERRORS, RETURN_ALL_ERRORS).
- **VALIDATE**: Validate is a table function to view all errors encountred during a previous COPY INTO command.Validate accepts a job id of a previous query or the last load operation executed.
![Validate & Validation MODE](./../assets/validate_validation.PNG)
- File format options can be set on a named stage or COPY INTO statement. Explicitly declared file format options can all be rolled up into independent file format snwoflake objects.
- File Formats can be applied to both named stages and COPY INTO statements. if set on both COPY INTO will take precedence.
![File Format](./../assets/file_format.PNG)

# Snowpipe and Loading Best practises:
![Snowpipe](./../assets/snowpipe.PNG)
- Snowpipe is designed to load new data typically within a minute after a file notification is sent.
- Snowpipe is serverless feature, using snowflake managed compute resources to load data files.
- Snowpipe load history is stored in the metadata of the pipe for 14 days to prevent reloading the same files into a table.
- When a pipe is paused, event messages received for the pipe enter a limited retention period. The period is 14 days by default.
- Data loading best practices:
    - 100-250 MB compressed
    - Organize data by path
    - pre-sort data
    - once per minute
## File format
- File format option can be set on a named stage or COPY INTO statement.
- If specified on both named stages and COPY INTO statement. COPY INTO will take precedence.
- There are two methods for detecting when a new file has been uploaded to a stage:
    - Automating Snowpipe using cloud messaging (external stages only)
    - Calling Snowpipe rest api (Internal and external stages)
- Snowpipe is designed to load new data typically within a minute after a file notification is sent.
- Snowpipe is serverless feature, using snowflake managed resources to load data files not a user managed virtual warehouse.
- snowpipe load history is stored in the metadata of pipe for `14 days`.
## Data unloading
- Table data can be unloaded to a stage via the `COPY INTO` command.
- The GET command is used to download a staged file to the local file system `GET @MY_STAGE file://folder/files/:`
- By default, results unloaded to a stage using COPY INTO LOCATION command are split into multiple files (CSV, GZIP, UTF-8). All data files unloaded to internal stages are automatically encrypted using 128-bit keys.
- COPY INTO includes a `PARTITION BY` copy option to partition unloaded data into a directory structure.
- GET cannot be executed for external stages.
- `VARIANT` is universal semi-structured data type of snowflake for loading data in semi-structured data fromats.
- Snowflake stores the VARIANT type internally in an efficient compressed columnar binary representation.
- VARIANT data type can hold up to 16MB compressed data per row.
- Variant column can contain SQL nulls and VARIANT nulls which are stored as string containing the word null.
- Semi Structured Data types:
    - ARRAY: `ARRAY_CONSTRUCT(value, value2, value3)`
    - OBJECT: `OBJECT_CONSTRUCT('key','value', 'key2', 'value2')`
    - VARIANT: Universal semi structured data type used to represent arbitrary data structures.
![Semi-Structured data formats](./../assets/semi_structured_data_fromats.png)
## Loading and Unloading semi-structured data
- `STRIP_OUTER_ARRAY`: Only used for loading. If true, JSON parser will remove outer brackets [].
- `STRIP_NULL_VALUES`: Only used for loading. If true, JSON parser will remove object fields or array elements containing NULL.
- `INFER_SCHEMA`: Automatic SCHEMA detection
![ACCESS SEMI STRUCTURED DATA](./../assets/access_semi_structured_data.png)
## SUPPORTED FUNCTIONS TYPES
![SUPPORTED FUNCTION TYPES](./../assets/function_types.png)
- **SCALAR Functions**: are used for returning one value per invocation. These are mostly used for returning one value per row. 
- **AGGREGATE FUNCTIONS**: Aggregate functions operate on values across rows to perform mathematical calculations such as sum average & counting.
- **WINDOW FUNCTIONS**: Subset of aggregate functions, allowing us to aggregate on a subset of rows used as input to a function. `MAX(AMOUNT) OVER (PARITION BY ACCOUNT_ID)`
- **TABLE FUNCTIONS**: return a set of rows for each input row. The returned set can contain zero one, or more rows. Each row can contain one or more columns.
- **SYSTEM FUNCTIONS**: provide a way to execute actions in the system. `select SYSTEM$cancel_query('query_id')` or provide information about the system `SELECT system$pipe_status('my_pipe')`

<br>

## ESTIMATION FUNCTION TYPES
- **Cardinality Estimation**: Estimates the number of distinct values.
![cardinality](./../assets/cardinality_estimation.png)
- **Similarity Estimation**: Estimates similarity between two or more sets.
![Similarity1](./../assets/similarity1.png)
![Similarity2](./../assets/similarity2.png)
- **Frequency Estimation**: Estimate frequency of values in a set.
![Frequency](./../assets/frequency.png)
- **Percentile Estimation**: Estimate percentile of values in a set. Percentile is staistical method to express what percentage of a set of values is below a certain value.
![Percentile](./../assets/percentile.png)
## Table sampling:
- Table sampling is a convenient way to read a random subset of rows from a table. `SYSTEM/BLOCK` or `BERNOULLI/ROW`.
## File functions:
 ![File Functions](./../assets/filefunctions.png)
- **Scoped file URL**: `build_scoped_file_url(stage_name, path_to_file)`. URL is valid for 24 Hours. The caller must have `USAGE` on External named stage or `READ` on internal named stage. If called in UDF, stored procedure or view does not require any privileges.
- **Stage File URL** `build_stage_file_url(stage_name, path_to_file)`. URL never expires.
- **Presigned URL**: `get_presigned_url(stage_name, path_to_file, expiration_time)`:The caller must have `USAGE` on External named stage or `READ` on internal named stage.
## Directory Tables:
```conf
CREATE STAGE INT_STAGE
DIRECTORY =  (ENABLE = TRUE)
```
![Directory Table](./../assets/directory_table.png)
- Directory tables must be refreshed to reflect the most up to date changes made to stage contents. This include new files being uploaded, removed files and changes to files in the path.
```conf
ALTER STAGE STAGE_NAME REFRESH;
```
## FILE SUPPORT REST API