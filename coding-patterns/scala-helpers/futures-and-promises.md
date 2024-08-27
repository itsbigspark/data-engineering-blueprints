# Futures and Promises in Scala

## Introduction
Futures and Promises are powerful abstractions in Scala for handling asynchronous computations. They allow you to write non-blocking code that can handle operations that may take time to complete, such as network requests or file I/O.

## Futures
A `Future` represents a value that may not yet be available. It is a placeholder for a result that will be computed asynchronously.

### Creating a Future
You can create a Future using the `Future` companion object. Here's an example:

    ```scala 
    import scala.concurrent.{Future, Await}
    import scala.concurrent.duration._
    import scala.concurrent.ExecutionContext.Implicits.global
    val futureResult: Future[Int] = Future {
      // Simulate a long computation
      Thread.sleep(1000)
      42
    }
    ```

### Handling Future Results
You can handle the result of a Future using methods like `map`, `flatMap`, and `onComplete`.

    ```scala
    futureResult.onComplete {
      case Success(value) => println(s"Result: $value")
      case Failure(e) => println(s"Error: ${e.getMessage}")
    }
    ```

### Composing Futures
Futures can be composed to create more complex asynchronous workflows.

    ```scala
    val future1 = Future { 1 }
    val future2 = Future { 2 }
    val combinedFuture = for {
      a <- future1
      b <- future2
    } yield a + b
    combinedFuture.onComplete {
      case Success(value) => println(s"Combined Result: $value")
      case Failure(e) => println(s"Error: ${e.getMessage}")
    }
    ```

## Promises
A `Promise` is a writable, single-assignment container that can be completed with a value or an exception. It is used to create a Future that can be completed later.

### Creating a Promise
You can create a Promise using the `Promise` companion object.

    ```scala
    import scala.concurrent.Promise
    val promise = Promise[Int]
    val futureFromPromise: Future[Int] = promise.future
    ```

### Completing a Promise
You can complete a Promise with a value or an exception.

    ```scala
    promise.success(42) // Completes the promise with a value
    // or
    promise.failure(new Exception("An error occurred")) // Completes with an error
    ```

### Using Promises
Promises are useful when you need to control the completion of a Future.

    ```scala
    import scala.concurrent.ExecutionContext.Implicits.global
    val promise = Promise[Int]
    val future = promise.future
    future.onComplete {
      case Success(value) => println(s"Promise completed with: $value")
      case Failure(e) => println(s"Promise failed with: ${e.getMessage}")
    }
    // Complete the promise later
    promise.success(100)
    ```

## Conclusion
Futures and Promises in Scala provide a robust way to handle asynchronous programming. They allow you to write clean, non-blocking code that can efficiently manage long-running tasks. Understanding how to use them effectively is crucial for building responsive applications.