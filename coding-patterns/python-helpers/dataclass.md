# Dataclasses in Python

Dataclasses are a feature in Python that automatically adds generated special methods such as `__init__()` and `__repr__()` to user-defined classes. They're designed to store data and provide a clean, concise way to create classes that are primarily used to store values.

## Benefits of Dataclasses

1. Reduced boilerplate code
2. Automatic generation of special methods
3. Easy to read and maintain
4. Built-in support for type hinting

## Example

Here's an example of using dataclasses to represent social media data:

```python
from dataclasses import dataclass, field
from datetime import datetime
from typing import List, Optional

@dataclass
class User:
    id: int
    username: str
    email: str
    created_at: datetime = field(default_factory=datetime.now)
    is_active: bool = True

@dataclass
class Post:
    id: int
    user: User
    content: str
    created_at: datetime = field(default_factory=datetime.now)
    likes: int = 0
    comments: List[str] = field(default_factory=list)

@dataclass
class SocialMediaAnalytics:
    platform: str
    users: List[User]
    posts: List[Post]
    total_likes: int = field(init=False)
    average_posts_per_user: float = field(init=False)

    def __post_init__(self):
        self.total_likes = sum(post.likes for post in self.posts)
        self.average_posts_per_user = len(self.posts) / len(self.users) if self.users else 0

# Usage
if __name__ == "__main__":
    user1 = User(1, "john_doe", "john@example.com")
    user2 = User(2, "jane_smith", "jane@example.com")

    post1 = Post(1, user1, "Hello, world!", likes=10)
    post2 = Post(2, user2, "Python is awesome!", likes=15)
    post3 = Post(3, user1, "Dataclasses are cool!", likes=20)

    analytics = SocialMediaAnalytics("MyPlatform", [user1, user2], [post1, post2, post3])

    print(f"Platform: {analytics.platform}")
    print(f"Total likes: {analytics.total_likes}")
    print(f"Average posts per user: {analytics.average_posts_per_user:.2f}")
    print(f"First user: {analytics.users[0]}")
    print(f"First post: {analytics.posts[0]}")
```

This example demonstrates how dataclasses can be used to represent complex data structures for social media analytics. The `User`, `Post`, and `SocialMediaAnalytics` classes are all defined using dataclasses, showcasing features like default values, derived fields, and post-initialisation processing.
