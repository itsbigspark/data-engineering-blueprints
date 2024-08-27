# Type Classes in Scala

Type classes are a powerful feature in Scala that allow you to add new functionality to existing types without modifying their source code. They are particularly useful in data engineering for creating generic algorithms that work with various data types.

## Key Concepts

1. **Type Class**: A trait that defines some behavior.
2. **Type Class Instances**: Implementations of the type class for specific types.
3. **Interface Objects**: Objects that provide a user-friendly API for the type class.
4. **Implicit Parameters**: Used to automatically pass type class instances to methods.

## Example

Here's an example of using type classes to create a generic data validation system:

```scala
import scala.util.Try

// Type Class
trait Validator[T] {
  def validate(value: T): Either[String, T]
}

// Type Class Instances
object ValidatorInstances {
  implicit val stringValidator: Validator[String] = new Validator[String] {
    def validate(value: String): Either[String, String] = 
      if (value.nonEmpty) Right(value) else Left("String is empty")
  }

  implicit val intValidator: Validator[Int] = new Validator[Int] {
    def validate(value: Int): Either[String, Int] = 
      if (value >= 0) Right(value) else Left("Number is negative")
  }

  implicit def optionValidator[T](implicit v: Validator[T]): Validator[Option[T]] = 
    new Validator[Option[T]] {
      def validate(value: Option[T]): Either[String, Option[T]] = 
        value match {
          case Some(x) => v.validate(x).map(Some(_))
          case None => Left("Option is empty")
        }
    }

  implicit def listValidator[T](implicit v: Validator[T]): Validator[List[T]] = 
    new Validator[List[T]] {
      def validate(value: List[T]): Either[String, List[T]] = 
        value.foldLeft[Either[String, List[T]]](Right(List.empty)) {
          case (Right(acc), elem) => v.validate(elem).map(acc :+ _)
          case (Left(err), _) => Left(err)
        }
    }
}

// Interface Object
object Validator {
  def validate[T](value: T)(implicit v: Validator[T]): Either[String, T] = v.validate(value)

  def validateAll[T](values: T*)(implicit v: Validator[T]): Either[String, List[T]] = 
    values.toList.foldLeft[Either[String, List[T]]](Right(List.empty)) {
      case (Right(acc), elem) => v.validate(elem).map(acc :+ _)
      case (Left(err), _) => Left(err)
    }
}

// Usage
object TypeClassExample extends App {
  import ValidatorInstances._
  import Validator._

  // Validating simple types
  println(validate("Hello"))  // Right(Hello)
  println(validate(""))       // Left(String is empty)
  println(validate(42))       // Right(42)
  println(validate(-1))       // Left(Number is negative)

  // Validating complex types
  println(validate(Some("Hello")))  // Right(Some(Hello))
  println(validate(None: Option[String]))  // Left(Option is empty)
  println(validate(List(1, 2, 3)))  // Right(List(1, 2, 3))
  println(validate(List(1, -2, 3)))  // Left(Number is negative)

  // Validating multiple values
  println(validateAll("Hello", "World"))  // Right(List(Hello, World))
  println(validateAll("Hello", "", "World"))  // Left(String is empty)

  // Custom validator for a case class
  case class Person(name: String, age: Int)

  implicit val personValidator: Validator[Person] = new Validator[Person] {
    def validate(person: Person): Either[String, Person] = 
      for {
        _ <- validate(person.name)
        _ <- validate(person.age)
      } yield person
  }

  println(validate(Person("Alice", 30)))  // Right(Person(Alice,30))
  println(validate(Person("", 30)))       // Left(String is empty)
  println(validate(Person("Bob", -1)))    // Left(Number is negative)
}
```

This example demonstrates how to use type classes to create a flexible and extensible validation system. The `Validator` type class defines the validation behavior, and we provide instances for various types including `String`, `Int`, `Option`, and `List`. We also show how to create a custom validator for a case class.

Type classes are particularly useful in data engineering scenarios where you need to write generic algorithms that work with various data types, such as:

1. Data validation and cleaning
2. Serialisation and deserialisation
3. Data transformation pipelines
4. Generic data processing algorithms

By using type classes, you can write more reusable and composable code, which is especially valuable when dealing with complex data processing tasks.