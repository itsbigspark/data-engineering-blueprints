# Functional Programming in Scala

Scala is a language that supports both object-oriented and functional programming paradigms. Here are some key concepts and examples of functional programming in Scala.

## Key Concepts

1. Immutability
2. Pure functions
3. Higher-order functions
4. Pattern matching
5. Recursion

## Examples

### Immutability and Pure Functions

```scala
case class Person(name: String, age: Int)

def incrementAge(person: Person): Person = {
  person.copy(age = person.age + 1)
}

val john = Person("John", 30)
val olderJohn = incrementAge(john)

println(john)      // Person(John,30)
println(olderJohn) // Person(John,31)
```

### Higher-order Functions

```scala
def applyTwice(f: Int => Int, x: Int): Int = f(f(x))

val addOne = (x: Int) => x + 1
val result = applyTwice(addOne, 5)

println(result) // 7
```

### Pattern Matching

```scala
sealed trait Shape
case class Circle(radius: Double) extends Shape
case class Rectangle(width: Double, height: Double) extends Shape

def area(shape: Shape): Double = shape match {
  case Circle(r) => Math.PI * r * r
  case Rectangle(w, h) => w * h
}

val circle = Circle(5)
val rectangle = Rectangle(4, 3)

println(area(circle))    // 78.53981633974483
println(area(rectangle)) // 12.0
```

### Recursion and Tail Recursion

```scala
import scala.annotation.tailrec

def factorial(n: Int): BigInt = {
  @tailrec
  def factorialTailRec(n: Int, acc: BigInt): BigInt = {
    if (n <= 1) acc
    else factorialTailRec(n - 1, n * acc)
  }
  
  factorialTailRec(n, 1)
}

println(factorial(5))  // 120
println(factorial(20)) // 2432902008176640000
```

### Option and Either for Error Handling

```scala
def divide(a: Int, b: Int): Option[Int] = {
  if (b == 0) None
  else Some(a / b)
}

def safeDivide(a: Int, b: Int): Either[String, Int] = {
  if (b == 0) Left("Division by zero")
  else Right(a / b)
}

println(divide(10, 2))     // Some(5)
println(divide(10, 0))     // None

println(safeDivide(10, 2)) // Right(5)
println(safeDivide(10, 0)) // Left(Division by zero)
```

### Functional Data Processing with Collections

```scala
val numbers = List(1, 2, 3, 4, 5)

val doubled = numbers.map(_ * 2)
val evens = numbers.filter(_ % 2 == 0)
val sum = numbers.foldLeft(0)(_ + _)

println(doubled) // List(2, 4, 6, 8, 10)
println(evens)   // List(2, 4)
println(sum)     // 15

// More advanced example
case class Person(name: String, age: Int)

val people = List(
  Person("Alice", 25),
  Person("Bob", 30),
  Person("Charlie", 35),
  Person("David", 40)
)

val averageAge = people.map(_.age).sum.toDouble / people.length
val namesOfPeopleOver30 = people.filter(_.age > 30).map(_.name)

println(f"Average age: $averageAge%.2f")           // Average age: 32.50
println(s"People over 30: $namesOfPeopleOver30")   // People over 30: List(Charlie, David)
```

These examples demonstrate various aspects of functional programming in Scala, including immutability, pure functions, higher-order functions, pattern matching, recursion, and functional data processing with collections.