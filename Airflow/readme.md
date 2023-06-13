# Apache Airflow Introduction:
- Apache Airflow allow you to create reliable and powerful data pipelines in a distributed environment while monitoring your DAGs.
- In airflow everything is developed in python.
-  Airflow is an open source platfom to `programmatically author, schedule` and `monitor` workflows.
# Apache Airflow Componenets:
- **Web Server**: Flask Server with Gunicorn serving the UI.
- **Scheduler**: Daemon in charge of scheduling workflows.
- **Metastore**: Database where metadata are stored.
- **Executor**: Class defining how your tasks should be executed.
- **Worker**: Process/sub process `executing` your task.
# Core Concepts:
- **`DAG`**: Directed Acyclic Graph is the core concept of Airflow, collecting Tasks together, organized with dependencies and relationships to say how they should run.
- **Operator**: an object encapsulating the task, the job that you want to execute. Basically an operator is a task in your DAG, in your data pipeline.
    - Action operator: In charge of executing something. For example, you want to execute a python function you use the python operator, you want to execute a bash command you execute the bash operator (postgresql operator etc.)
    - Transfer Operator: In charge of transferring data from a source to a destination.
    - Sensor Operator: allows you to wait for something to hapen before moving forward, before getting completed. For example you want to wait your file to land on your filesystem you use file sensor.
- **Task instance**: is an instance of your operator in your DAG.
- **Workflow**: combination of all concepts give you a workflow.
![Workflow](./../assets/airflow/workflow.png)
- Airflow is not a data streaming solution neither a data processing framework. That means you should not process terabytes of data in your DAG.
- Configuring the scheduler:
    - **min_file_process_interval**: Number of seconds after which a DAG file is parsed. The DAG is parsed every x number of seconds.
    - **dag_dir_list_interval**: How often in seconds to scan directory for new files. Default to 5 minutes 
- Configuring the web server:
    - **worker_refresh_interval**: Number of seconds to wait before refreshing a batch of workers. default to 30 seconds.
# Airflow CLI commands:
- `airflow init db`: this initializes the metastore.
- `airflow db reset`: delete all metadata from the metastore.
- `airflow webserver`: runs the webserver of airflow
- `airflow scheduler`: runs airflow scheduler
- `airflow celery worker`: this command allows you to declare a machine as airflow worker
- `airflow dags list`: list dags
- `airflow dags trigger example_operator`
- `airflow tasks test dag_id task_id execution_date`: verify if your tasks work without checking its dependencies.
## What is a Dag?
```python
from airflow import DAG
from datetime import datetime, timedelta
default_args={ //these arguments are for your task
    "owner": "airflow",
    "email_on_failure":False,
    "email_on_retry": False,
    "email": "admin@localhost.com"
    "retries":1 //if a task fails then it will retry one time
    "retry_delay": timedelta(minutes=5) //wait 5 mins before retry
}
with DAG("dag_id", start_date=datetime(2021, 1, 1), schedule_interval="@daily", default_args=default_args, catchup=False) as dag:
//catchup will prevent running all the previous dag runs between the current date and the start date
```
## What is an Operator?
- `Operator = Task`:
- `Action Operator`: Allow you to execute something.
- `Transfer Operator`: Transfer data from source to destination.
- `Sensor Operator`: Allow you to wait for something to happen before moving to the next task.
## Airflow Practice:
- `airflow_plugins_folder`: refers to the directory where custom plugins can be placed. Plugins in Airflow provide additional functionalities and extend the core features of the platform. This folder is where you can store Python modules or packages that define custom operators, sensors, hooks, macros or other components to enhance the capabilities of your Airflow environment.
- `airflow_dags_folder`: refers to the directory where you can store your DAG files. DAGs are Python scripts that define the workflows and tasks to be executed by airflow. AIRFLOW monitors this folder for any changes and automatically detects and loads the new or modified DAG files.
```bash
airflow tasks test dag_id task_id execution_date(2021-01-01)
# Testing that the task work without storing any metadata in the metadatastore

```
