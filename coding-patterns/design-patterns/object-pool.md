# Object Pool Pattern

The Object Pool pattern is used to manage object caching. It can significantly improve performance in situations where the cost of initialising a class instance is high and the rate of instantiation of a class is high.

## When to Use

Use the Object Pool pattern when:
- The cost of initialising an instance of the class is high
- The rate of instantiation of a class is high
- The number of instances in use at any one time is low

## Example

Here's an example of a database connection pool using the Object Pool pattern:

```python
import time
from queue import Queue

class DatabaseConnection:
    def __init__(self, connection_string):
        self.connection_string = connection_string
        self.connected = False

    def connect(self):
        # Simulate expensive connection process
        time.sleep(1)
        self.connected = True
        print(f"Connected to {self.connection_string}")

    def execute(self, query):
        if not self.connected:
            raise Exception("Not connected to the database")
        print(f"Executing query: {query}")

    def close(self):
        self.connected = False
        print("Connection closed")

class DatabaseConnectionPool:
    def __init__(self, connection_string, pool_size):
        self.connection_string = connection_string
        self.pool_size = pool_size
        self.pool = Queue(maxsize=pool_size)
        self._create_pool()

    def _create_pool(self):
        for _ in range(self.pool_size):
            connection = DatabaseConnection(self.connection_string)
            connection.connect()
            self.pool.put(connection)

    def get_connection(self):
        return self.pool.get()

    def release_connection(self, connection):
        self.pool.put(connection)

# Usage
if __name__ == "__main__":
    pool = DatabaseConnectionPool("mysql://localhost:3306/mydb", 3)

    # Use connections
    conn1 = pool.get_connection()
    conn1.execute("SELECT * FROM users")
    pool.release_connection(conn1)

    conn2 = pool.get_connection()
    conn2.execute("INSERT INTO users (name) VALUES ('John')")
    pool.release_connection(conn2)

    # This will reuse one of the previous connections
    conn3 = pool.get_connection()
    conn3.execute("UPDATE users SET name = 'Jane' WHERE id = 1")
    pool.release_connection(conn3)
```

This example demonstrates how the Object Pool pattern can be used to manage a pool of database connections. The `DatabaseConnectionPool` class creates and manages a fixed number of `DatabaseConnection` objects, allowing them to be reused efficiently.
