# Full Snapshot Pull

## Description
Full Snapshot Pull is a data extraction pattern that involves pulling the entire dataset from a source system at regular intervals. This method is simple to implement and tracks historical changes effectively. It's particularly useful for dimensional data or smaller datasets where the entire content needs to be refreshed periodically.

## Constraints/Challenges
- Slow and potentially high storage costs, especially for large datasets.
- Not well-suited for large fact data or frequently changing datasets.
- Requires significant network bandwidth and processing power.
- May cause increased load on source systems during extraction.
- Potential for data inconsistencies if the source data changes during the extraction process.

## Implementation Example
Here's a Python example using Apache Airflow and pandas for a Full Snapshot Pull pattern:

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta
import pandas as pd
import sqlalchemy

def extract_full_snapshot():
    # Connect to the source database
    engine = sqlalchemy.create_engine('postgresql://user:password@host:port/database')
    
    # Extract the full dataset
    df = pd.read_sql_table('source_table', engine)
    
    # Add a timestamp column to track when the snapshot was taken
    df['snapshot_timestamp'] = datetime.now()
    
    # Write the full snapshot to the destination
    df.to_parquet(f'/path/to/snapshots/full_snapshot_{datetime.now().strftime("%Y%m%d_%H%M%S")}.parquet')

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
    'full_snapshot_pull',
    default_args=default_args,
    description='A DAG to perform full snapshot pull',
    schedule_interval=timedelta(days=1),
)

full_snapshot_task = PythonOperator(
    task_id='extract_full_snapshot',
    python_callable=extract_full_snapshot,
    dag=dag,
)

full_snapshot_task
```

## Mermaid Diagram
```mermaid
graph TD
    A[Start Full Snapshot Pull] --> B[Connect to Source System]
    B --> C[Begin Transaction]
    C --> D[Extract Full Dataset]
    D --> E{Extraction Complete?}
    E -->|No| F[Handle Partial Extraction]
    F --> G[Log Error]
    G --> H[Retry or Abort]
    E -->|Yes| I[Add Metadata]
    I --> J[Commit Transaction]
    J --> K[Write to Storage]
    K --> L{Storage Successful?}
    L -->|No| M[Retry Write]
    M --> N[Log Error]
    N --> O[Notify Admin]
    L -->|Yes| P[Update Catalog/Metadata]
    P --> Q[Trigger Downstream Processes]
    Q --> R[Archive Old Snapshots]
    R --> S[Log Completion]
    S --> T[End Full Snapshot Pull]

    U[Monitoring] --> V{Performance Issues?}
    V -->|Yes| W[Optimise Query]
    V -->|No| X[Continue]
    W --> Y[Adjust Resource Allocation]
    Y --> Z[Update Documentation]

    AA[Error Handling] --> AB{Error Type}
    AB -->|Connection Failure| AC[Retry with Backoff]
    AB -->|Data Integrity| AD[Validate Source Data]
    AB -->|Storage Full| AE[Clean Up Old Snapshots]
    AC --> AF[Log and Notify]
    AD --> AF
    AE --> AF
```

## Notes and References
- Full snapshot pulls are ideal for dimensional data or smaller datasets that require complete refreshes.
- Consider implementing a change data capture (CDC) system for large or frequently changing datasets to reduce load and improve efficiency.
- Use compression techniques to reduce storage costs and network bandwidth usage.
- Implement a robust error handling and retry mechanism to deal with network issues or source system unavailability.
- Consider using a staging area to store the snapshot before finalising it in the destination to ensure data consistency.

### Best Practices
1. **Scheduling**: Choose an appropriate frequency for full snapshot pulls based on data volatility and business requirements.
2. **Versioning**: Implement a versioning system for snapshots to easily track and manage historical data.
3. **Parallel Processing**: For large datasets, consider implementing parallel extraction to improve performance.
4. **Data Validation**: Implement checksums or row counts to ensure the integrity of the extracted data.
5. **Incremental Loading**: Consider combining full snapshot pulls with incremental loads to optimise performance and resource usage.

### Additional References:
- [Data Warehousing Fundamentals](https://www.amazon.com/Data-Warehousing-Fundamentals-Comprehensive-Professionals/dp/0471412546) by Paulraj Ponniah
- [The Data Warehouse Toolkit](https://www.amazon.com/Data-Warehouse-Toolkit-Definitive-Dimensional/dp/1118530802) by Ralph Kimball and Margy Ross
- [Airflow Documentation](https://airflow.apache.org/docs/)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [Data Engineering Patterns](https://www.databricks.com/blog/2021/06/09/data-engineering-patterns-data-pipelines.html) by Databricks


