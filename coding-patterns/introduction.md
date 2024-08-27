# Introduction

Using the appropriate code design pattern can make your code easy to read, extensible, and seamless to modify existing logic, debug, and enable developers to onboard quicker. This guide will cover the typical code design patterns for building data pipelines, addressing questions such as:

- What design patterns do people follow when writing code for a typical data pipeline?
- What do people mean by abstract and concrete implementation?
- Why do data engineers prefer writing functional code?
- What do the terms Factory, Strategy, Singleton, Object pools mean, and when to use them?

By the end of this guide, you will have an overview of the typical code design patterns used for building data pipelines. You'll learn about the pros and cons of each design pattern, when to use them, and more importantly, when not to use them.

## Example: Functional vs Object-Oriented Approach

Let's look at a simple example to illustrate the difference between functional and object-oriented approaches in Python:

### Functional Approach

```python
def extract_data(api_key):
    # Simulated data extraction
    return [1, 2, 3, 4, 5]

def transform_data(data):
    return [x * 2 for x in data]

def load_data(data, destination):
    print(f"Data loaded to {destination}: {data}")

def etl_pipeline(api_key, destination):
    extracted_data = extract_data(api_key)
    transformed_data = transform_data(extracted_data)
    load_data(transformed_data, destination)

# Usage
etl_pipeline("my_api_key", "database")
```

### Object-Oriented Approach

```python
class ETLPipeline:
    def __init__(self, api_key, destination):
        self.api_key = api_key
        self.destination = destination

    def extract_data(self):
        # Simulated data extraction
        return [1, 2, 3, 4, 5]

    def transform_data(self, data):
        return [x * 2 for x in data]

    def load_data(self, data):
        print(f"Data loaded to {self.destination}: {data}")

    def run(self):
        extracted_data = self.extract_data()
        transformed_data = self.transform_data(extracted_data)
        self.load_data(transformed_data)

# Usage
pipeline = ETLPipeline("my_api_key", "database")
pipeline.run()
```

These examples demonstrate two different approaches to structuring an ETL pipeline. The functional approach uses separate functions for each step, while the object-oriented approach encapsulates the pipeline in a class. Each has its advantages, and the choice between them often depends on the specific requirements of your project.
