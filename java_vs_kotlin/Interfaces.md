# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🔌 Interfaces

### Default Methods

**Java:**

```java
// Java interface with default and static methods
public interface UserRepository {
    // Abstract method - must be implemented
    User findById(Long id);

    // Default method provides implementation
    default User findByIdOrThrow(Long id) {
        return Optional.ofNullable(findById(id))
                .orElseThrow(() -> new RuntimeException("User not found"));
    }

    // Factory method in interface
    static UserRepository inMemory() {
        return new InMemoryUserRepository();
    }
}
```

**Kotlin:**

```kotlin
// Kotlin interface with default implementations
interface UserRepository {
    // Abstract function returns nullable User
    fun findById(id: Long): User?

    // Default implementation using Elvis operator
    fun findByIdOrThrow(id: Long): User =
        findById(id) ?: throw RuntimeException("User not found")

    // Companion object for static-like members
    companion object {
        // Factory function
        fun inMemory(): UserRepository = InMemoryUserRepository()
    }
}
```

**Key Differences:**

- Kotlin uses nullable types instead of Optional
- More concise default implementations
- Companion object replaces static methods
- Elvis operator for null checking
- Function return types are after the name

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
