# Testing with ScalaTest

ScalaTest is a popular testing framework for Scala that provides a flexible and expressive way to write tests. It supports multiple testing styles and integrates well with various build tools and IDEs.

## Key Concepts

1. **Test Styles**: ScalaTest offers multiple styles like FunSuite, FlatSpec, WordSpec, etc.
2. **Matchers**: A domain-specific language for writing assertions.
3. **Fixtures**: Reusable setup and teardown code.
4. **Property-based Testing**: Generate test cases automatically.

## Writing Tests

Here's a comprehensive example of using ScalaTest to test a simple ETL pipeline:

```scala
import org.scalatest._
import org.scalatest.flatspec.AnyFlatSpec
import org.scalatest.matchers.should.Matchers
import org.scalatest.prop.TableDrivenPropertyChecks

// Class to be tested
case class ETLPipeline(
  extract: () => List[String],
  transform: List[String] => List[Int],
  load: List[Int] => Unit
) {
  def run(): Unit = {
    val extracted = extract()
    val transformed = transform(extracted)
    load(transformed)
  }
}

class ETLPipelineSpec extends AnyFlatSpec with Matchers with TableDrivenPropertyChecks {

  // Sample transformation function
  def stringToInt(strings: List[String]): List[Int] = strings.map(_.toInt)

  "ETLPipeline" should "correctly process data" in {
    var loadedData: List[Int] = List.empty

    val pipeline = ETLPipeline(
      extract = () => List("1", "2", "3"),
      transform = stringToInt,
      load = (data: List[Int]) => loadedData = data
    )

    pipeline.run()

    loadedData should be(List(1, 2, 3))
  }

  it should "handle empty input" in {
    var loadedData: List[Int] = List.empty

    val pipeline = ETLPipeline(
      extract = () => List.empty,
      transform = stringToInt,
      load = (data: List[Int]) => loadedData = data
    )

    pipeline.run()

    loadedData should be(empty)
  }

  it should "throw NumberFormatException for invalid input" in {
    val pipeline = ETLPipeline(
      extract = () => List("1", "two", "3"),
      transform = stringToInt,
      load = (_: List[Int]) => ()
    )

    an [NumberFormatException] should be thrownBy pipeline.run()
  }

  // Property-based test
  it should "always produce output with the same length as input" in {
    forAll(Table(
      ("input", "expected"),
      (List("1", "2", "3"), 3),
      (List.empty[String], 0),
      (List("10", "20"), 2)
    )) { (input, expected) =>
      var loadedData: List[Int] = List.empty

      val pipeline = ETLPipeline(
        extract = () => input,
        transform = stringToInt,
        load = (data: List[Int]) => loadedData = data
      )

      pipeline.run()

      loadedData.length should be(expected)
    }
  }
}

// Run tests
object RunTests extends App {
  org.scalatest.run(new ETLPipelineSpec)
}
```

This example demonstrates several key features of ScalaTest:

1. Using `AnyFlatSpec` for a readable, flat specification style.
2. Using `Matchers` for expressive assertions.
3. Testing happy path, edge cases, and exception scenarios.
4. Using property-based testing with `TableDrivenPropertyChecks`.

To run these tests, you need to add ScalaTest to your project dependencies. If you're using sbt, add the following to your `build.sbt`:
```scala
libraryDependencies += "org.scalatest" %% "scalatest" % "3.2.10" % Test
```

Then you can run the tests using:

```bash
sbt test
```

ScalaTest provides many more features like custom matchers, async testing, and integration with mocking frameworks. This example serves as a starting point for writing comprehensive tests for your Scala data engineering projects.