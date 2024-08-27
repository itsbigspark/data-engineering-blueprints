# Case Classes in Scala

Case classes in Scala are a powerful feature that automatically adds useful methods to your classes. They're ideal for creating immutable data structures and are commonly used in pattern matching.

## Benefits of Case Classes

1. Immutability by default
2. Automatic generation of equals, hashCode, and toString methods
3. Pattern matching support
4. Easy to use in collections

## Example

Here's an example of using case classes to represent social media data:

```scala
import java.time.LocalDateTime

case class User(
  id: Int,
  username: String,
  email: String,
  createdAt: LocalDateTime = LocalDateTime.now(),
  isActive: Boolean = true
)

case class Post(
  id: Int,
  user: User,
  content: String,
  createdAt: LocalDateTime = LocalDateTime.now(),
  likes: Int = 0,
  comments: List[String] = List.empty
)

case class SocialMediaAnalytics(
  platform: String,
  users: List[User],
  posts: List[Post]
) {
  lazy val totalLikes: Int = posts.map(_.likes).sum
  lazy val averagePostsPerUser: Double = 
    if (users.nonEmpty) posts.size.toDouble / users.size else 0
}

// Usage
object CaseClassExample extends App {
  val user1 = User(1, "john_doe", "john@example.com")
  val user2 = User(2, "jane_smith", "jane@example.com")

  val post1 = Post(1, user1, "Hello, world!", likes = 10)
  val post2 = Post(2, user2, "Scala is awesome!", likes = 15)
  val post3 = Post(3, user1, "Case classes are cool!", likes = 20)

  val analytics = SocialMediaAnalytics("MyPlatform", List(user1, user2), List(post1, post2, post3))

  println(s"Platform: ${analytics.platform}")
  println(s"Total likes: ${analytics.totalLikes}")
  println(f"Average posts per user: ${analytics.averagePostsPerUser}%.2f")
  println(s"First user: ${analytics.users.head}")
  println(s"First post: ${analytics.posts.head}")

  // Pattern matching
  analytics.posts.foreach {
    case Post(_, User(_, username, _), content, _, likes, _) if likes > 10 =>
      println(s"Popular post by $username: $content")
    case _ => // Do nothing
  }
}
```

This example demonstrates how case classes can be used to represent complex data structures for social media analytics. It showcases features like default values, lazy vals for derived fields, and pattern matching.