# Singleton Pattern

The Singleton pattern ensures a class has only one instance and provides a global point of access to it.

## When to Use

Use the Singleton pattern when:
- Exactly one instance of a class is needed to coordinate actions across the system
- You need a global point of access to that instance

## Pros and Cons

Pros:
- Ensures that a class has just a single instance
- Provides a global access point to that instance

Cons:
- Violates the Single Responsibility Principle
- Can make unit testing difficult
- Can be considered an anti-pattern in some contexts

## Example

Here's an example of a Singleton pattern implementation for a configuration manager:

```python
class ConfigManager:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.config = {}
        return cls._instance

    def set(self, key, value):
        self.config[key] = value

    def get(self, key):
        return self.config.get(key)

# Usage
config1 = ConfigManager()
config1.set("DEBUG", True)

config2 = ConfigManager()
print(config2.get("DEBUG"))  # Output: True

print(config1 is config2)  # Output: True
```



