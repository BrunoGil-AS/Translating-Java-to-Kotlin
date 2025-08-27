# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🚫 Error Handling

### Exception Philosophy

**Java:**

- Uses checked exceptions for expected error conditions
- Requires explicit exception declaration and handling
- Runtime exceptions for programming errors

```java
public class FileProcessor {
    // Must declare checked exceptions
    public String readConfig() throws IOException {
        try {
            return Files.readString(Path.of("config.txt"));
        } catch (IOException e) {
            log.error("Failed to read config", e);
            throw e; // Must be declared or caught
        }
    }

    public void processData(String data) {
        // RuntimeException doesn't need declaration
        if (data == null) {
            throw new IllegalArgumentException("Data cannot be null");
        }
    }
}
```

**Kotlin:**

- All exceptions are unchecked
- Focus on expressing failures through the type system
- Result type pattern for expected failures

```kotlin
class FileProcessor {
    // No throws declaration needed
    fun readConfig(): Result<String> = runCatching {
        File("config.txt").readText()
    }.onFailure { error ->
        log.error("Failed to read config", error)
    }

    fun processData(data: String?) {
        // Use require for preconditions
        require(data != null) { "Data cannot be null" }

        // Use check for business rules
        check(data.isNotBlank()) { "Data cannot be empty" }
    }
}
```

### Error Type Hierarchy

**Java:**

```java
public class ErrorHandling {
    public void demonstrateHierarchy() {
        try {
            throw new Exception("Base"); // Checked
        } catch (RuntimeException e) {
            // Unchecked exceptions
        } catch (IOException e) {
            // Checked exceptions
        } catch (Exception e) {
            // Base class for all checked exceptions
        } finally {
            // Cleanup
        }
    }
}
```

**Kotlin:**

```kotlin
class ErrorHandling {
    fun demonstrateHierarchy() {
        try {
            throw Exception("Base")
        } catch (e: RuntimeException) {
            // All exceptions are unchecked
        } catch (e: Exception) {
            // Catch all exceptions
        } finally {
            // Cleanup
        }
    }
}
```

### Functional Error Handling

**Java:**

```java
public class FunctionalErrors {
    public Optional<Integer> divideNumbers(int a, int b) {
        try {
            return Optional.of(a / b);
        } catch (ArithmeticException e) {
            return Optional.empty();
        }
    }

    public void processResult() {
        divideNumbers(10, 0)
            .ifPresentOrElse(
                result -> System.out.println("Result: " + result),
                () -> System.out.println("Division failed")
            );
    }
}
```

**Kotlin:**

```kotlin
class FunctionalErrors {
    fun divideNumbers(a: Int, b: Int): Result<Int> =
        runCatching { a / b }

    fun processResult() {
        divideNumbers(10, 0)
            .fold(
                onSuccess = { println("Result: $it") },
                onFailure = { println("Division failed: ${it.message}") }
            )
    }

    // Using sealed class for custom error handling
    sealed class ComputationError {
        object DivisionByZero : ComputationError()
        data class Overflow(val value: Long) : ComputationError()
    }

    fun safeCompute(a: Int, b: Int): Either<ComputationError, Int> =
        when {
            b == 0 -> Either.Left(ComputationError.DivisionByZero)
            a.toLong() * b > Int.MAX_VALUE ->
                Either.Left(ComputationError.Overflow(a.toLong() * b))
            else -> Either.Right(a * b)
        }
}
```

### Best Practices and Utilities

**Java:**

```java
public class ErrorUtils {
    public static <T> T tryOrDefault(Supplier<T> operation, T defaultValue) {
        try {
            return operation.get();
        } catch (Exception e) {
            return defaultValue;
        }
    }

    public static void tryWithResources(Resource resource,
                                      Consumer<Resource> operation) {
        try (resource) {
            operation.accept(resource);
        } catch (Exception e) {
            throw new RuntimeException("Operation failed", e);
        }
    }
}
```

**Kotlin:**

```kotlin
object ErrorUtils {
    fun <T> tryOrDefault(operation: () -> T, defaultValue: T): T =
        runCatching { operation() }.getOrDefault(defaultValue)

    fun <T : AutoCloseable, R> T.useAndMap(block: (T) -> R): R =
        use { resource -> block(resource) }

    // Extension function for better error context
    fun <T> Result<T>.withContext(context: String): Result<T> =
        onFailure { error ->
            error.addSuppressed(Exception("Failed in context: $context"))
        }

    // Retry mechanism with exponential backoff
    suspend fun <T> withRetry(
        times: Int = 3,
        initialDelay: Long = 100,
        block: suspend () -> T
    ): Result<T> = runCatching {
        var currentDelay = initialDelay
        repeat(times - 1) {
            try {
                return@runCatching block()
            } catch (e: Exception) {
                delay(currentDelay)
                currentDelay *= 2
            }
        }
        block()
    }
}
```

**Key Differences:**

1. **Exception Checking:**

   - Java enforces checked exception handling
   - Kotlin treats all exceptions as unchecked
   - Kotlin encourages using the type system for error cases

2. **Error Handling Patterns:**

   - Java relies on try-catch blocks
   - Kotlin provides functional approaches with `Result`
   - Kotlin supports sealed classes for custom error types
   - Kotlin's smart casts work in catch blocks

3. **Resource Management:**

   - Java uses try-with-resources
   - Kotlin uses the `use` function
   - Kotlin's scope functions provide cleaner syntax

4. **Error Context:**

   - Java stack traces require manual enrichment
   - Kotlin can extend Result type for better context
   - Kotlin's coroutines provide structured concurrency

5. **Functional Approaches:**

   - Java uses Optional for some error cases
   - Kotlin's Result type is more powerful
   - Kotlin supports railway-oriented programming
   - Better support for composition in Kotlin

6. **Performance:**
   - No performance overhead in Kotlin vs Java
   - Both use same underlying JVM mechanisms
   - Kotlin's inline functions can reduce overhead
   - Smart casts avoid redundant checks
