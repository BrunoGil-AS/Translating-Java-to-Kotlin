# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧮 Generic Types

### List<T>, Map<K, V>

- Both languages have strong support for generics for type-safe collections.

**Java:**

```java
List<String> names = new ArrayList<String>();
Map<Integer, String> userMap = new HashMap<>();
```

**Kotlin:**

```kotlin
val names: List<String> = listOf("Alice", "Bob")
val userMap: Map<Int, String> = mapOf(1 to "Alice")
```

### Wildcards (? extends, ? super) vs Kotlin variance (out, in)

- **Java** uses wildcards (`? extends T` for covariance, `? super T` for contravariance) to handle generic type variance.
- **Kotlin** uses declaration-site variance (`out` for covariance, `in` for contravariance) which is often simpler to work with.

**Java (Covariance):**

```java
// This list can hold Number or any subtype of Number
List<? extends Number> numbers = new ArrayList<Integer>();
```

**Kotlin (Covariance with `out`):**

```kotlin
// The 'out' keyword makes the type parameter covariant
interface Source<out T> {
    fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // This is allowed because T is 'out'
}
```
