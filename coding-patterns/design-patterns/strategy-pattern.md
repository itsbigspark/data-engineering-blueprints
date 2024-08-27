# Strategy Pattern

The Strategy pattern is a behavioral design pattern that lets you define a family of algorithms, put each of them into a separate class, and make their objects interchangeable.

## When to Use

Use the Strategy pattern when:
- You want to use different variants of an algorithm within an object and be able to switch from one algorithm to another during runtime
- You have many related classes that differ only in their behavior
- You need to isolate the business logic of a class from the implementation details of algorithms that may not be as important in the context of that logic

## Example

Here's an example of using the Strategy pattern for different data transformation strategies in a data pipeline:

```python
from abc import ABC, abstractmethod
import pandas as pd

# Abstract Strategy
class TransformationStrategy(ABC):
    @abstractmethod
    def transform(self, data: pd.DataFrame) -> pd.DataFrame:
        pass

# Concrete Strategies
class NormaliseStrategy(TransformationStrategy):
    def transform(self, data: pd.DataFrame) -> pd.DataFrame:
        return (data - data.mean()) / data.std()

class ScaleStrategy(TransformationStrategy):
    def transform(self, data: pd.DataFrame) -> pd.DataFrame:
        return (data - data.min()) / (data.max() - data.min())

class LogTransformStrategy(TransformationStrategy):
    def transform(self, data: pd.DataFrame) -> pd.DataFrame:
        return data.apply(lambda x: np.log(x) if x.dtype != 'object' else x)

# Context
class DataPipeline:
    def __init__(self, strategy: TransformationStrategy):
        self._strategy = strategy

    def set_strategy(self, strategy: TransformationStrategy):
        self._strategy = strategy

    def transform_data(self, data: pd.DataFrame) -> pd.DataFrame:
        return self._strategy.transform(data)

# Usage
if __name__ == "__main__":
    # Sample data
    data = pd.DataFrame({
        'A': [1, 2, 3, 4, 5],
        'B': [10, 20, 30, 40, 50],
        'C': [100, 200, 300, 400, 500]
    })

    # Create pipeline with initial strategy
    pipeline = DataPipeline(NormaliseStrategy())

    # Transform data using different strategies
    print("Normalised data:")
    print(pipeline.transform_data(data))

    pipeline.set_strategy(ScaleStrategy())
    print("\nScaled data:")
    print(pipeline.transform_data(data))

    pipeline.set_strategy(LogTransformStrategy())
    print("\nLog-transformed data:")
    print(pipeline.transform_data(data))
```

This example demonstrates how the Strategy pattern can be used to implement different data transformation algorithms in a data pipeline. The `DataPipeline` class can switch between different transformation strategies at runtime, allowing for flexible and extensible data processing.
