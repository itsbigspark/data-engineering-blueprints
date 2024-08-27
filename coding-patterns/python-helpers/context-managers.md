# Context Managers in Python

Context managers in Python are a convenient way to manage resources, ensuring proper acquisition and release of resources like file handles, network connections, or database connections. They are typically used with the `with` statement.

## Benefits of Context Managers

1. Automatic resource management
2. Cleaner, more readable code
3. Ensures resources are properly closed or released, even if exceptions occur

## Creating a Context Manager

There are two main ways to create a context manager:

1. Using a class with `__enter__` and `__exit__` methods
2. Using the `@contextmanager` decorator

Here are examples of both approaches for managing database connections:

```python
import sqlite3
from contextlib import contextmanager

# Method 1: Using a class
class DatabaseConnection:
    def __init__(self, db_name):
        self.db_name = db_name
        self.conn = None

    def __enter__(self):
        self.conn = sqlite3.connect(self.db_name)
        return self.conn

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.conn:
            if exc_type:
                self.conn.rollback()
            else:
                self.conn.commit()
            self.conn.close()
        return False  # Re-raise any exceptions

# Method 2: Using @contextmanager decorator
@contextmanager
def database_connection(db_name):
    conn = sqlite3.connect(db_name)
    try:
        yield conn
    except Exception:
        conn.rollback()
        raise
    else:
        conn.commit()
    finally:
        conn.close()

# Usage examples
if __name__ == "__main__":
    # Using the class-based context manager
    with DatabaseConnection("example.db") as conn:
        cursor = conn.cursor()
        cursor.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
        cursor.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
        print("Inserted user using class-based context manager")

    # Using the decorator-based context manager
    with database_connection("example.db") as conn:
        cursor = conn.cursor()
        cursor.execute("INSERT INTO users (name) VALUES (?)", ("Bob",))
        print("Inserted user using decorator-based context manager")

    # Demonstrating exception handling
    try:
        with DatabaseConnection("example.db") as conn:
            cursor = conn.cursor()
            cursor.execute("INSERT INTO non_existent_table (name) VALUES (?)", ("Charlie",))
    except sqlite3.OperationalError as e:
        print(f"Caught exception: {e}")
        print("Changes were rolled back")

    # Verifying the results
    with database_connection("example.db") as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM users")
        users = cursor.fetchall()
        print("Users in the database:", users)
```

This example demonstrates both class-based and decorator-based context managers for managing SQLite database connections. It shows how to:

1. Create a context manager using a class with `__enter__` and `__exit__` methods.
2. Create a context manager using the `@contextmanager` decorator.
3. Use context managers to ensure proper connection handling, including committing successful transactions and rolling back failed ones.
4. Handle exceptions within context managers.

Both methods achieve the same goal of safely managing database connections, but they offer different levels of flexibility and readability depending on your specific use case.
