# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🔌 Interfaces

### Default Methods

**Java:**

```java
public interface UserRepository {
    User findById(Long id);

    // Default method in Java
    default User findByIdOrThrow(Long id) {
        return Optional.ofNullable(findById(id))
                .orElseThrow(() -> new RuntimeException("User not found"));
    }

    // Static method in interface (Java 8+)
    static UserRepository inMemory() {
        return new InMemoryUserRepository();
    }
}
```

**Kotlin:**

```kotlin
interface UserRepository {
    fun findById(id: Long): User?

    // Default implementation in Kotlin
    fun findByIdOrThrow(id: Long): User =
        findById(id) ?: throw RuntimeException("User not found")

    companion object {
        // Similar to static methods in Java
        fun inMemory(): UserRepository = InMemoryUserRepository()
    }
}
```

### Functional Interfaces

**Java:**

```java
@FunctionalInterface
public interface Processor<T> {
    void process(T input);
}

@FunctionalInterface
public interface Transformer<T, R> {
    R transform(T input);

    default Transformer<T, R> andThen(Transformer<R, R> after) {
        return input -> after.transform(transform(input));
    }
}

// Usage
public class ProcessorExample {
    public void example() {
        Processor<String> printer = System.out::println;
        Transformer<String, Integer> lengthCounter = String::length;
    }
}
```

**Kotlin:**

```kotlin
// Type alias for simple function types
typealias Processor<T> = (T) -> Unit
typealias Transformer<T, R> = (T) -> R

// Custom functional interface if needed
fun interface CustomProcessor<T> {
    fun process(input: T)
}

// Usage
class ProcessorExample {
    fun example() {
        val printer: Processor<String> = ::println
        val lengthCounter: Transformer<String, Int> = String::length

        // Lambda syntax
        val customProcessor = CustomProcessor { input: String ->
            println(input)
        }
    }
}
```

### Marker Interfaces

**Java:**

```java
// Marker interface - no methods
public interface Serializable {}

public interface Cloneable {}

// Custom marker
public interface Remote {}

public class Document implements Serializable, Cloneable {
    // Implementation
}
```

**Kotlin:**

```kotlin
// Marker interface
interface Remote

// Interface with no methods
interface Identifiable {
    // Can have properties
    val id: String
}

// Custom marker with type parameter
interface Cacheable<T>

class Document : Serializable, Cloneable, Cacheable<Document> {
    // Implementation
}
```

### Properties in Interfaces

**Java:**

```java
public interface Configuration {
    // Constants in Java interface
    String DEFAULT_HOST = "localhost";

    String getHost();
    void setHost(String host);
}
```

**Kotlin:**

```kotlin
interface Configuration {
    // Properties in Kotlin interface
    val defaultHost: String
        get() = "localhost"

    // Abstract property
    var host: String

    // Default implementation using property
    fun getConnectionString(): String = "http://$host"
}
```

**Key Differences:**

- Kotlin interfaces can have properties
- No need for `@FunctionalInterface` annotation
- Type aliases for simple function types
- More concise default implementations
- Properties can have backing fields in interfaces
- Companion objects instead of static methods
- Multiple interface delegation with `by`
- No distinction between method/property declaration
