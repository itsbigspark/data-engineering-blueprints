# Data Pipeline Design Patterns

## Purpose

This repository serves as a comprehensive guide to various data pipeline design patterns. It aims to provide data engineers, architects, and developers with a structured overview of different approaches to building efficient, scalable, and maintainable data pipelines.

The patterns are organised into categories, each focusing on specific aspects of data pipeline design:

1. Source Patterns
2. Sink Patterns
3. Extraction Patterns
4. Behavioral Patterns
5. Structural Patterns

Additionally, a decision-making blueprint is included to help guide the selection of appropriate patterns based on specific use cases and requirements.

## Repository Structure

## Example Implementations

To illustrate these patterns, we provide example implementations in both Python and Scala. Here's a simple example of a data pipeline pattern in both languages:

### Python Example: Simple ETL Pipeline

```python
import pandas as pd

def extract():
    # Simulating data extraction
    return pd.DataFrame({'id': range(1, 6), 'value': [10, 20, 30, 40, 50]})

def transform(data):
    # Simple transformation: doubling the 'value' column
    data['value'] = data['value'] * 2
    return data

def load(data):
    # Simulating data loading (just printing in this case)
    print("Transformed data:")
    print(data)

def etl_pipeline():
    data = extract()
    transformed_data = transform(data)
    load(transformed_data)

if __name__ == "__main__":
    etl_pipeline()
```

### Scala Example: Simple ETL Pipeline

```scala
import org.apache.spark.sql.{SparkSession, DataFrame}

object SimplePipeline {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession.builder()
      .appName("SimplePipeline")
      .master("local[*]")
      .getOrCreate()

    import spark.implicits._

    def extract(): DataFrame = {
      // Simulating data extraction
      Seq((1, 10), (2, 20), (3, 30), (4, 40), (5, 50))
        .toDF("id", "value")
    }

    def transform(data: DataFrame): DataFrame = {
      // Simple transformation: doubling the 'value' column
      data.withColumn("value", $"value" * 2)
    }

    def load(data: DataFrame): Unit = {
      // Simulating data loading (just showing in this case)
      println("Transformed data:")
      data.show()
    }

    def etlPipeline(): Unit = {
      val data = extract()
      val transformedData = transform(data)
      load(transformedData)
    }

    etlPipeline()
    spark.stop()
  }
}
```

These examples demonstrate a basic ETL (Extract, Transform, Load) pipeline pattern. As we explore more complex patterns in this repository, we'll provide more sophisticated examples for each.
