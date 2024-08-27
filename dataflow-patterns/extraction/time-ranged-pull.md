# Time Ranged Pull

## Description
Time Ranged Pull is a data extraction pattern that involves pulling data for a specific time frame from a source system. This method is efficient for incremental data loading and is particularly useful for processing historical data or updating data warehouses. It allows for parallelisation of data extraction and can significantly reduce the load on source systems compared to full snapshot pulls.

## Constraints/Challenges
- Incremental loads can be challenging to implement correctly, especially when dealing with updates and deletes.
- Non-replayable sources may cause inconsistencies if data changes after the initial extraction.
- Handling time zone differences and daylight saving time transitions can lead to data duplication or loss.
- Dealing with data that arrives late or out of order relative to its event time.
- Ensuring consistency across multiple time-ranged pulls, especially when dealing with overlapping time periods.
- Determining the appropriate time range size for optimal performance and data freshness.

## Implementation Example
Here's a Python example using Apache Airflow and SQLAlchemy for a Time Ranged Pull pattern:

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta
import sqlalchemy
import pandas as pd

def time_ranged_pull(ds, **kwargs):
    # Connect to the source database
    engine = sqlalchemy.create_engine('postgresql://user:password@host:port/database')
    
    # Define the time range
    end_date = datetime.strptime(ds, '%Y-%m-%d')
    start_date = end_date - timedelta(days=1)
    
    # Extract data for the specified time range
    query = f"""
    SELECT *
    FROM source_table
    WHERE event_timestamp >= '{start_date}'
      AND event_timestamp < '{end_date}'
    """
    df = pd.read_sql(query, engine)
    
    # Process and store the extracted data
    process_and_store_data(df)

def process_and_store_data(df):
    # Implement your data processing and storage logic here
    pass

default_args = {
    'owner': 'data_engineer',
    'depends_on_past': False,
    'start_date': datetime(2023, 1, 1),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

dag = DAG(
    'time_ranged_pull',
    default_args=default_args,
    description='A DAG to perform time ranged pull',
    schedule_interval=timedelta(days=1),
)

time_ranged_pull_task = PythonOperator(
    task_id='time_ranged_pull',
    python_callable=time_ranged_pull,
    provide_context=True,
    dag=dag,
)

time_ranged_pull_task
```

## Mermaid Diagram
```mermaid
graph TD
    A[Start Time Ranged Pull] --> B[Define Time Range]
    B --> C[Connect to Source System]
    C --> D[Extract Data for Time Range]
    D --> E{Extraction Complete?}
    E -->|No| F[Handle Partial Extraction]
    F --> G[Log Error]
    G --> H[Retry or Skip]
    E -->|Yes| I[Validate Extracted Data]
    I --> J{Data Valid?}
    J -->|No| K[Log Data Quality Issue]
    K --> L[Apply Data Cleansing]
    J -->|Yes| M[Transform Data]
    L --> M
    M --> N[Load Data to Target]
    N --> O[Update Metadata/Watermarks]
    O --> P[Trigger Dependent Processes]
    P --> Q[Archive Processed Data]
    Q --> R[Log Completion]
    R --> S[End Time Ranged Pull]

    T[Error Handling] --> U{Error Type}
    U -->|Connection Failure| V[Retry with Backoff]
    U -->|Data Integrity| W[Validate Source Data]
    U -->|Processing Error| X[Partial Reprocessing]
    V --> Y[Log Error and Notify]
    W --> Y
    X --> Y

    Z[Monitoring] --> AA{Performance Issues?}
    AA -->|Yes| AB[Optimise Query]
    AA -->|No| AC[Continue]
    AB --> AD[Adjust Time Range Sise]
    AD --> AE[Update Documentation]

    AF[Incremental Processing] --> AG[Identify Changed Records]
    AG --> AH[Handle Updates]
    AH --> AI[Handle Deletes]
    AI --> AJ[Merge with Existing Data]

    AK[Time Zone Handling] --> AL[Normalise to UTC]
    AL --> AM[Handle DST Transitions]

    AN[Late Arriving Data] --> AO[Implement Grace Period]
    AO --> AP[Reprocess Affected Ranges]

    AQ[Parallelisation] --> AR[Split Time Range]
    AR --> AS[Distribute to Workers]
    AS --> AT[Merge Results]
```

## Notes and References
- Time-ranged pulls are efficient for daily updates and historical data loading.
- Implement proper error handling and retry mechanisms for failed pulls.
- Use a centralised scheduler (e.g., Apache Airflow) for coordinating time-ranged pulls across multiple sources.
- Consider implementing a backfill mechanism for handling missed or corrupted data pulls.
- Use watermarks or checkpoints to track the last successfully processed time range.
- Implement data quality checks to ensure the integrity of extracted data.

### Best Practices
1. **Idempotency**: Ensure that your time-ranged pull process is idempotent, allowing for safe retries and reprocessing without duplicating data.
2. **Scalability**: Design your system to handle varying time range sizes and data volumes efficiently.
3. **Monitoring**: Implement comprehensive monitoring to track the progress of time-ranged pulls and detect any anomalies.
4. **Time Zone Management**: Standardise on a single time zone (preferably UTC) for all time-based operations to avoid inconsistencies.
5. **Data Reconciliation**: Periodically perform full data reconciliation to catch any discrepancies that might have been missed during incremental loads.

### Additional References:
- [Airflow: Time-based Scheduling](https://airflow.apache.org/docs/apache-airflow/stable/scheduler.html#dag-runs)
- [Incremental Data Loading](https://www.databricks.com/glossary/incremental-data-load)
- [Handling Time Zones in Data Pipelines](https://www.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/ch07.html#time_zones)
- [Dealing with Late and Out-of-Order Data](https://www.oreilly.com/library/view/streaming-systems/9781491983867/ch02.html)
- [Change Data Capture (CDC) Techniques](https://www.redhat.com/en/topics/integration/what-is-change-data-capture)
- [Data Warehouse Toolkit](https://www.amazon.com/Data-Warehouse-Toolkit-Definitive-Dimensional/dp/1118530802) by Ralph Kimball and Margy Ross