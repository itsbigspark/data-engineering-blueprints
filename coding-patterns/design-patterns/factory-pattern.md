# Factory Pattern

The Factory pattern is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.

## When to Use

Use the Factory pattern when:
- You have multiple similar ETL pipelines (e.g., for different social media platforms)
- You want to provide a standard interface for creating different but related objects
- You want to decouple object creation from the code that uses the object

## Example

Here's an example of a factory for creating ETL objects for different social media platforms:

```python
from abc import ABC, abstractmethod

# Abstract ETL class
class ETL(ABC):
    @abstractmethod
    def extract(self):
        pass

    @abstractmethod
    def transform(self):
        pass

    @abstractmethod
    def load(self):
        pass

# Concrete ETL classes
class TwitterETL(ETL):
    def extract(self):
        print("Extracting data from Twitter API")

    def transform(self):
        print("Transforming Twitter data")

    def load(self):
        print("Loading Twitter data into database")

class FacebookETL(ETL):
    def extract(self):
        print("Extracting data from Facebook API")

    def transform(self):
        print("Transforming Facebook data")

    def load(self):
        print("Loading Facebook data into database")

# ETL Factory
class ETLFactory:
    @staticmethod
    def create_etl(platform):
        if platform.lower() == "twitter":
            return TwitterETL()
        elif platform.lower() == "facebook":
            return FacebookETL()
        else:
            raise ValueError(f"Unsupported platform: {platform}")

# Usage
if __name__ == "__main__":
    platforms = ["Twitter", "Facebook"]
    
    for platform in platforms:
        etl = ETLFactory.create_etl(platform)
        etl.extract()
        etl.transform()
        etl.load()
        print()
```

This example demonstrates how the Factory pattern can be used to create different ETL objects for various social media platforms. The `ETLFactory` class provides a single point of object creation, allowing easy extension for new platforms without modifying existing code.
