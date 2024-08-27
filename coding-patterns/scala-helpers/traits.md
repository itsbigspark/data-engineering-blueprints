# Traits in Scala

Traits in Scala are similar to interfaces in Java, but they can also contain implemented methods and fields. They're a powerful way to achieve multiple inheritance and compose behavior.

## Key Features of Traits

1. Can contain both abstract and concrete methods
2. Support multiple inheritance
3. Can be used for mixin composition
4. Can have constructor parameters (Scala 3)

## Example

Here's an example of using traits to define different aspects of a data processing pipeline:

```scala
import scala.util.Try

// Define traits for different stages of data processing
trait Extractor[T] {
  def extract(): Try[T]
}

trait Transformer[T, U] {
  def transform(data: T): Try[U]
}

trait Loader[T] {
  def load(data: T): Try[Unit]
}

// Concrete implementations
class FileExtractor(filePath: String) extends Extractor[String] {
  override def extract(): Try[String] = Try {
    scala.io.Source.fromFile(filePath).getLines().mkString("\n")
  }
}

class JsonTransformer extends Transformer[String, Map[String, Any]] {
  override def transform(data: String): Try[Map[String, Any]] = Try {
    // Simplified JSON parsing, use a proper JSON library in real-world scenarios
    data.split("\n")
      .map(line => line.split(":"))
      .map { case Array(key, value) => key.trim -> value.trim }
      .toMap
  }
}

class DatabaseLoader(dbUrl: String) extends Loader[Map[String, Any]] {
  override def load(data: Map[String, Any]): Try[Unit] = Try {
    println(s"Simulating loading data to database at $dbUrl")
    println(s"Data: $data")
  }
}

// Composing traits to create a complete ETL pipeline
class ETLPipeline[T, U, V](
  extractor: Extractor[T],
  transformer: Transformer[T, U],
  loader: Loader[V]
) {
  def run(): Try[Unit] = for {
    extracted <- extractor.extract()
    transformed <- transformer.transform(extracted)
    _ <- loader.load(transformed.asInstanceOf[V])
  } yield ()
}

// Usage
object TraitExample extends App {
  val filePath = "data.txt"
  val dbUrl = "jdbc:postgresql://localhost:5432/mydb"

  val pipeline = new ETLPipeline(
    new FileExtractor(filePath),
    new JsonTransformer,
    new DatabaseLoader(dbUrl)
  )

  pipeline.run() match {
    case scala.util.Success(_) => println("ETL process completed successfully")
    case scala.util.Failure(exception) => println(s"ETL process failed: ${exception.getMessage}")
  }
}
```

This example demonstrates how traits can be used to define different components of a data processing pipeline. The `ETLPipeline` class composes these traits to create a complete ETL process.