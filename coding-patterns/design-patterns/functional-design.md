# Functional Design

Functional design is a programming paradigm that emphasises writing code as a series of pure functions. In data engineering, functional design principles can lead to more maintainable and testable code.

## Key Principles

1. **Atomicity**: A function should only do one task.
2. **Idempotency**: If you run the code multiple times with the same input, the output should be the same.
3. **No side effects**: A function should not affect any external data besides its output.
4. **Immutability**: Data should not be modified after creation; instead, create new data structures.
5. **Higher-order functions**: Functions that can take other functions as arguments or return functions.

## Benefits in Data Engineering

- **Testability**: Pure functions are easier to test as they always produce the same output for a given input.
- **Parallelisation**: Functions without side effects can be easily parallelised.
- **Debugging**: With immutable data and no side effects, it's easier to trace the flow of data through your pipeline.
- **Composability**: Small, focused functions can be combined to create complex data transformations.


## Example in Python

Here's an example of a functional load function in Python:

```python
from typing import List, Dict
import pandas as pd

def load_data(data: List[Dict[str, str]]) -> pd.DataFrame:
    """
    Load data into a pandas DataFrame.
    
    Args:
    data (List[Dict[str, str]]): List of dictionaries containing the data.
    
    Returns:
    pd.DataFrame: DataFrame containing the loaded data.
    """
    return pd.DataFrame(data)

def transform_data(df: pd.DataFrame) -> pd.DataFrame:
    """
    Transform the data by adding a new column.
    
    Args:
    df (pd.DataFrame): Input DataFrame.
    
    Returns:
    pd.DataFrame: Transformed DataFrame.
    """
    return df.assign(full_name=lambda x: x['first_name'] + ' ' + x['last_name'])

def filter_data(df: pd.DataFrame, min_age: int) -> pd.DataFrame:
    """
    Filter the data based on a minimum age.
    
    Args:
    df (pd.DataFrame): Input DataFrame.
    min_age (int): Minimum age for filtering.
    
    Returns:
    pd.DataFrame: Filtered DataFrame.
    """
    return df[df['age'] >= min_age]

def process_data(data: List[Dict[str, str]], min_age: int) -> pd.DataFrame:
    """
    Process the data using functional composition.
    
    Args:
    data (List[Dict[str, str]]): Input data.
    min_age (int): Minimum age for filtering.
    
    Returns:
    pd.DataFrame: Processed DataFrame.
    """
    return (
        data
        | load_data
        | transform_data
        | (lambda df: filter_data(df, min_age))
    )

# Example usage
input_data = [
    {"first_name": "John", "last_name": "Doe", "age": "30"},
    {"first_name": "Jane", "last_name": "Smith", "age": "25"},
    {"first_name": "Bob", "last_name": "Johnson", "age": "35"}
]

result = process_data(input_data, 28)
print(result)
```

## Example in Scala

Here's an equivalent example in Scala using Apache Spark:

```scala
import org.apache.spark.sql.{DataFrame, SparkSession}
import org.apache.spark.sql.functions._

object FunctionalETL {
  def loadData(data: Seq[Map[String, String]])(implicit spark: SparkSession): DataFrame = {
    import spark.implicits._
    data.toDF()
  }

  def transformData(df: DataFrame): DataFrame = {
    df.withColumn("full_name", concat(col("first_name"), lit(" "), col("last_name")))
  }

  def filterData(df: DataFrame, minAge: Int): DataFrame = {
    df.filter(col("age") >= minAge)
  }

  def processData(data: Seq[Map[String, String]], minAge: Int)(implicit spark: SparkSession): DataFrame = {
    loadData(data)
      .transform(transformData)
      .transform(df => filterData(df, minAge))
  }

  def main(args: Array[String]): Unit = {
    implicit val spark: SparkSession = SparkSession.builder()
      .appName("FunctionalETL")
      .master("local[*]")
      .getOrCreate()

    val inputData = Seq(
      Map("first_name" -> "John", "last_name" -> "Doe", "age" -> "30"),
      Map("first_name" -> "Jane", "last_name" -> "Smith", "age" -> "25"),
      Map("first_name" -> "Bob", "last_name" -> "Johnson", "age" -> "35")
    )

    val result = processData(inputData, 28)
    result.show()

    spark.stop()
  }
}
```

Both examples demonstrate functional design principles by using pure functions, composing them together, and avoiding side effects.

By following these functional design principles, we create a data pipeline that is easy to understand, test, and modify. Each step of the transformation is clearly defined and can be individually tested or replaced without affecting the rest of the pipeline.