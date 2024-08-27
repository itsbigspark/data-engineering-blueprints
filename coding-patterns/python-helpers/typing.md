# Typing in Python

Python's type hinting system allows developers to specify expected types for variables, function parameters, and return values. While Python remains a dynamically typed language, type hints can help catch type-related errors early and improve code readability.

## Benefits of Type Hinting

1. Improved code readability
2. Better IDE support (auto-completion, refactoring)
3. Catch type-related errors early with static type checkers like mypy

## Comprehensive Type Hinting Example

Here's a comprehensive example demonstrating various aspects of type hinting in Python:

```python
from typing import List, Dict, Tuple, Optional, Union, Callable
from datetime import datetime

# Basic type hints
def greet(name: str) -> str:
    return f"Hello, {name}!"

# Complex types
def process_data(data: List[Dict[str, Union[int, str]]]) -> Tuple[int, float]:
    total = sum(item['value'] for item in data if isinstance(item['value'], int))
    average = total / len(data)
    return total, average

# Optional and Union types
def parse_date(date_string: Optional[str] = None) -> Union[datetime, None]:
    if date_string:
        return datetime.strptime(date_string, "%Y-%m-%d")
    return None

# Type aliases
UserId = int
UserName = str
UserData = Dict[UserId, UserName]

def get_user(user_id: UserId, user_data: UserData) -> Optional[UserName]:
    return user_data.get(user_id)

# Callable types
TransformFunc = Callable[[int], int]

def apply_transform(numbers: List[int], transform: TransformFunc) -> List[int]:
    return [transform(num) for num in numbers]

# Generic types
from typing import TypeVar, Generic

T = TypeVar('T')

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: List[T] = []

    def push(self, item: T) -> None:
        self.items.append(item)

    def pop(self) -> T:
        return self.items.pop()

# Usage
if __name__ == "__main__":
    print(greet("Alice"))

    data = [{"name": "Alice", "value": 10}, {"name": "Bob", "value": 20}]
    total, avg = process_data(data)
    print(f"Total: {total}, Average: {avg}")

    print(parse_date("2023-05-17"))
    print(parse_date())

    user_data = {1: "Alice", 2: "Bob"}
    print(get_user(1, user_data))

    numbers = [1, 2, 3, 4, 5]
    squared = apply_transform(numbers, lambda x: x ** 2)
    print(squared)

    int_stack: Stack[int] = Stack()
    int_stack.push(1)
    int_stack.push(2)
    print(int_stack.pop())

    str_stack: Stack[str] = Stack()
    str_stack.push("hello")
    str_stack.push("world")
    print(str_stack.pop())
```

This example covers:
1. Basic type hints for function parameters and return values
2. Complex types like List, Dict, and Tuple
3. Optional and Union types
4. Type aliases
5. Callable types
6. Generic types

To check for type-related errors, you can use a static type checker like mypy:

```bash
mypy your_script.py
```

Remember that type hints are optional in Python and don't affect runtime behavior. They're primarily used for documentation, IDE support, and static type checking.
