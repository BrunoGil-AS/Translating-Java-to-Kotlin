# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🔌 Interfaces

Interfaces define **contracts** that classes can implement. Kotlin enhances interfaces with properties, default implementations, and companion objects.

### Default Methods

**Java:**

- Interfaces can have **default methods** with implementation, allowing API evolution without breaking existing classes.
- Static methods in interfaces act like **factory or utility methods**.

```java
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

- Interfaces can contain **abstract properties** and **default property implementations**.
- Uses **nullable types** instead of Optional.
- Companion objects replace static methods.
- Default implementations are concise using the **Elvis operator (`?:`)**.

```kotlin
interface UserRepository {

    // Abstract method - must be implemented
    fun findById(id: Long): User?

    // Default method provides implementation
    fun findByIdOrThrow(id: Long): User =
        findById(id) ?: throw RuntimeException("User not found")

    // Companion object replaces static methods
    companion object {
        fun inMemory(): UserRepository = InMemoryUserRepository()
    }
}
```

**Key Differences:**

- Kotlin has **nullable types** and the Elvis operator for null handling.
- Default implementations are more concise.
- Companion objects replace static methods.
- Function return types come **after the function name**.

---

### Functional Interfaces

- Functional interfaces are **interfaces with a single abstract method (SAM)**.
- In Kotlin, **functions are first-class citizens**, so you can use type aliases or `fun interface`.

**Java:**

```java
// Functional interface for processing data
@FunctionalInterface
public interface Processor<T> {
    void process(T input);
}

// Functional interface for transforming data
@FunctionalInterface
public interface Transformer<T, R> {
    R transform(T input);

    default Transformer<T, R> andThen(Transformer<R, R> after) {
        return input -> after.transform(transform(input));
    }
}

// Usage
Processor<String> printer = System.out::println;
Transformer<String, Integer> lengthCounter = String::length;
```

**Kotlin:**

```kotlin
// Type aliases for simple function types
typealias Processor<T> = (T) -> Unit
typealias Transformer<T, R> = (T) -> R

// Custom functional interface
fun interface CustomProcessor<T> {
    fun process(input: T)
}

val printer: Processor<String> = ::println
val lengthCounter: Transformer<String, Int> = String::length
val customProcessor = CustomProcessor { input: String -> println(input) }
```

**Key Differences:**

- Kotlin doesn't require `@FunctionalInterface`.
- Type aliases simplify function types.
- Lambda syntax is more concise.

---

### Marker Interfaces

- Marker interfaces are **empty interfaces** used to tag classes with metadata.

**Java:**

```java
public interface Serializable {}
public interface Cloneable {}
public interface Remote {}

public class Document implements Serializable, Cloneable {}
```

**Kotlin:**

```kotlin
interface Remote
interface Identifiable { val id: String }
interface Cacheable<T>

class Document : Serializable, Cloneable, Cacheable<Document> {}
```

- **Remote**: Marks a class as remote (e.g., for distributed systems).
  - Remote objects may be accessed over a network.
- **Identifiable**: Requires a unique identifier.
- **Cacheable**: Indicates that a class can be cached.

**Key Differences:**

- Kotlin interfaces can have properties.
- Marker interfaces can also use type parameters.

---

### Properties in Interfaces

Normally, interfaces cannot have state (i.e., fields), but they can have properties.

**Java:**

```java
public interface Configuration {
    String DEFAULT_HOST = "localhost";
    String getHost();
    void setHost(String host);
}
```

**Kotlin:**

```kotlin
interface Configuration {
    val defaultHost: String
        get() = "localhost"

    var host: String

    fun getConnectionString(): String = "http://$host"
}
```

**Key Differences:**

- Kotlin interfaces can contain **abstract and default properties**.
- Default implementations for functions can access interface properties.
- Companion objects replace static constants or methods.
- Multiple interface delegation is supported with `by`.
- No distinction between method and property declarations in Kotlin interfaces.
