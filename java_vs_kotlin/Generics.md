# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧮 Generic Types

### List<T>, Map<K, V>

- Both languages have strong support for generics for type-safe collections.

**Java:**

```java
// Basic generic collection declarations in Java
List<String> names = new ArrayList<String>();  // Explicit type
Map<Integer, String> userMap = new HashMap<>(); // Diamond operator
```

**Kotlin:**

```kotlin
// Kotlin's concise generic collection creation
val names: List<String> = listOf("Alice", "Bob")      // Type can be inferred
val userMap: Map<Int, String> = mapOf(1 to "Alice")   // Factory functions
```

**Key Differences:**

- Kotlin provides concise factory functions
- Type inference reduces verbosity
- Immutable collections by default
- More intuitive pair creation with 'to'

### Wildcards (? extends, ? super) vs Kotlin variance (out, in)

- **Java** uses wildcards (`? extends T` for covariance, `? super T` for contravariance) to handle generic type variance.
- **Kotlin** uses declaration-site variance (`out` for covariance, `in` for contravariance) which is often simpler to work with.

**Java (Covariance):**

```java
// Using wildcards for type variance in Java
public class NumberProcessor {
    // This list can hold Number or any subtype of Number
    List<? extends Number> numbers = new ArrayList<Integer>();

    // Producer - can only read from numbers
    public Number getFirst(List<? extends Number> nums) {
        return nums.get(0);
    }

    // Consumer - can only add to numbers
    public void addNumber(List<? super Integer> nums) {
        nums.add(42);
    }
}
```

**Kotlin (Covariance with `out`):**

```kotlin
// Using declaration-site variance in Kotlin
class NumberProcessor {
    // Producer interface with out modifier
    interface NumberSource<out T : Number> {
        fun getNext(): T    // Can only produce T
    }

    // Consumer interface with in modifier
    interface NumberSink<in T : Number> {
        fun process(item: T)  // Can only consume T
    }

    // Usage example
    fun demo() {
        val ints: NumberSource<Int> = IntSource()
        val nums: NumberSource<Number> = ints  // OK because of 'out'

        val numSink: NumberSink<Number> = NumberProcessor()
        val intSink: NumberSink<Int> = numSink // OK because of 'in'
    }
}
```

**Key Differences:**

- Java uses wildcard types (`? extends`, `? super`)
- Kotlin uses declaration-site variance (`out`, `in`)
- Kotlin's approach is more intuitive and safer
- Less verbose syntax in Kotlin
- Clearer producer/consumer relationships
