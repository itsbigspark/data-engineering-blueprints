# Python Decorators

## Introduction
Python decorators are a powerful tool that allows you to modify the behavior of a function or class. They are often used to add functionality to existing code in a clean and readable way.

## What is a Decorator?
A decorator is a function that takes another function as an argument, extends its behavior, and returns a new function. This is often used for logging, access control, memoisation, and more.

### Basic Syntax
```python
def my_decorator(func):
    def wrapper():
        # Code to execute before the function
        func()
        # Code to execute after the function
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")
```

## Use Cases in Data Engineering Pipelines

### 1. Logging
In data pipelines, tracking the flow of data and transformations is crucial. Decorators can be used to log the execution time of functions.

```python
import time

def log_execution_time(func):
    def wrapper(args, kwargs):
        start_time = time.time()
        result = func(args, kwargs)
        end_time = time.time()
        print(f"Execution time: {end_time - start_time} seconds")
        return result
    return wrapper

@log_execution_time
def process_data(data):
    # Data processing logic
    pass

### 2. Caching
Caching results of expensive function calls can significantly improve performance in data pipelines.

``` python
def cache_results(func):
    cache = {}
def wrapper(args):
    if args not in cache:
        cache[args] = func(args)
        return cache[args]
    return wrapper

@cache_results
def fetch_data(source):
    # Logic to fetch data from a source
    pass
```

### 3. Access Control
In a multi-user environment, decorators can enforce access control to certain functions.

``` python
def requires_authentication(func):
    def wrapper(user):
    if not user.is_authenticated:
        raise Exception("User not authenticated")
    return func(user)
    return wrapper

    @requires_authentication
    def access_sensitive_data(user):
    # Logic to access sensitive data
pass
```

## Conclusion
Decorators are a versatile feature in Python that can enhance the functionality of your data engineering pipelines. By using decorators for logging, caching, and access control, you can create more efficient and maintainable code.

## Further Reading
- [Python Official Documentation on Decorators](https://docs.python.org/3/glossary.html#term-decorator)
- [Real Python: Python Decorators 101](https://realpython.com/primer-on-python-decorators/)

