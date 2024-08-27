# Testing with pytest

pytest is a popular testing framework for Python that makes it easy to write simple and scalable test cases. It's particularly useful for testing data pipelines due to its powerful features and flexibility.

## Key Concepts

1. **Test Discovery**: pytest automatically discovers test files and functions based on naming conventions.
2. **Fixtures**: Reusable setup and teardown functions that can be shared across tests.
3. **Parameterisation**: Run the same test function with different inputs.
4. **Mocking**: Simulate behavior of complex objects or external systems.

## Writing Tests

Here's a comprehensive example of using pytest to test a simple ETL pipeline:

```python
import pytest
from unittest.mock import Mock, patch
import pandas as pd
from io import StringIO

# Function to be tested
def etl_pipeline(source_data, transformation_func, target):
    # Extract
    df = pd.read_csv(StringIO(source_data))
    
    # Transform
    df = transformation_func(df)
    
    # Load
    target.write(df.to_csv(index=False))

# Sample transformation function
def add_total_column(df):
    df['Total'] = df['A'] + df['B']
    return df

# Fixtures
@pytest.fixture
def sample_data():
    return "A,B\n1,2\n3,4\n5,6"

@pytest.fixture
def expected_output():
    return "A,B,Total\n1,2,3\n3,4,7\n5,6,11\n"

# Tests
def test_etl_pipeline(sample_data, expected_output):
    mock_target = Mock()
    etl_pipeline(sample_data, add_total_column, mock_target)
    mock_target.write.assert_called_once_with(expected_output)

@pytest.mark.parametrise("input_data,expected", [
    ("A,B\n1,2", "A,B,Total\n1,2,3\n"),
    ("A,B\n10,20", "A,B,Total\n10,20,30\n"),
])
def test_etl_pipeline_parameterised(input_data, expected):
    mock_target = Mock()
    etl_pipeline(input_data, add_total_column, mock_target)
    mock_target.write.assert_called_once_with(expected)

@patch('pandas.read_csv')
def test_etl_pipeline_mock_extract(mock_read_csv, expected_output):
    mock_read_csv.return_value = pd.DataFrame({'A': [1, 3, 5], 'B': [2, 4, 6]})
    mock_target = Mock()
    etl_pipeline("dummy_data", add_total_column, mock_target)
    mock_target.write.assert_called_once_with(expected_output)

# Run tests
if __name__ == "__main__":
    pytest.main([__file__])
```

This example demonstrates several key features of pytest:

1. Using fixtures (`sample_data` and `expected_output`) to provide reusable test data.
2. A basic test case (`test_etl_pipeline`) that checks if the ETL pipeline produces the expected output.
3. Parameterised testing (`test_etl_pipeline_parameterised`) to run the same test with different inputs.
4. Mocking external dependencies (`test_etl_pipeline_mock_extract`) to isolate the test from the actual data source.

To run these tests, save the code in a file named `test_etl_pipeline.py` and run `pytest test_etl_pipeline.py` in your terminal.
