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
![Semi-Structured data formats](./assets/semi_structured_data_fromats.png)
## Loading and Unloading semi-structured data
- `STRIP_OUTER_ARRAY`: Only used for loading. If true, JSON parser will remove outer brackets [].
- `STRIP_NULL_VALUES`: Only used for loading. If true, JSON parser will remove object fields or array elements containing NULL.
- `INFER_SCHEMA`: Automatic SCHEMA detection
![ACCESS SEMI STRUCTURED DATA](./assets/access_semi_structured_data.png)
## SUPPORTED FUNCTIONS TYPES
![SUPPORTED FUNCTION TYPES](./assets/function_types.png)
- **SCALAR Functions**: are used for returning one value per invocation. These are mostly used for returning one value per row. 
- **AGGREGATE FUNCTIONS**: Aggregate functions operate on values across rows to perform mathematical calculations such as sum average & counting.
- **WINDOW FUNCTIONS**: Subset of aggregate functions, allowing us to aggregate on a subset of rows used as input to a function. `MAX(AMOUNT) OVER (PARITION BY ACCOUNT_ID)`
- **TABLE FUNCTIONS**: return a set of rows for each input row. The returned set can contain zero one, or more rows. Each row can contain one or more columns.
- **SYSTEM FUNCTIONS**: provide a way to execute actions in the system. `select SYSTEM$cancel_query('query_id')` or provide information about the system `SELECT system$pipe_status('my_pipe')`

<br>

## ESTIMATION FUNCTION TYPES
