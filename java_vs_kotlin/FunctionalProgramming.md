[`<--`](../JavaVsKotlin.md)

## 🧠 Functional Programming

### Lambdas & Higher-Order Functions

**Java:**

- Functions are not first-class citizens but can be represented by functional interfaces (`Function`, `Predicate`, `Consumer`).

```java
public class HigherOrderFunctionExample {
    public void process(String value, Function<String, Integer> processor) {
        System.out.println(processor.apply(value));
    }

    public void usage() {
        process("hello", s -> s.length()); // 5
    }
}
```

**Kotlin:**

- Functions are first-class citizens. They can be stored in variables, passed as arguments, and returned from other functions.

```kotlin
// The function `processor` takes a String and returns an Int
fun process(value: String, processor: (String) -> Int) {
    println(processor(value))
}

fun usage() {
    process("hello") { it.length } // 5
}
```

### Immutability

- **Java**: Immutability is achieved by declaring fields `final` and using immutable collections (`List.of`).
- **Kotlin**: Promotes immutability with `val` (read-only) properties and first-class support for immutable collections (`List`, `Set`, `Map`).
