# Pattern Matching in Scala

## Introduction
Pattern matching is a powerful feature in Scala that allows you to match on the structure of data and decompose it. It is similar to switch statements in other languages but much more powerful and expressive. Pattern matching can be used with various data types, including case classes, tuples, lists, and more.

## Basic Syntax
The basic syntax of pattern matching in Scala involves the `match` keyword followed by a set of case statements. Each case statement defines a pattern and the corresponding code to execute if the pattern matches.


## Basic Syntax
The basic syntax of pattern matching in Scala involves the `match` keyword followed by a set of case statements. Each case statement defines a pattern and the corresponding code to execute if the pattern matches.

### Example 1: Matching on Integers

    ```scala
    val number = 2
    number match {
      case 1 => println("One")
      case 2 => println("Two")
      case _ => println("Not One or Two")
    }
    ```

### Example 2: Matching on Tuples
    ```scala
    val point = (1, 2)
    point match {
      case (0, 0) => println("Origin")
      case (x, 0) => println(s"X-axis at $x")
      case (0, y) => println(s"Y-axis at $y")
      case (x, y) => println(s"Point at ($x, $y)")
    }
    ```

### Example 3: Matching on Case Classes
    ```scala
    case class Person(name: String, age: Int)
    val person = Person("Alice", 25)
    person match {
      case Person("Alice", age) => println(s"Alice is $age years old")
      case Person(name, age) => println(s"$name is $age years old")
    }
    ```