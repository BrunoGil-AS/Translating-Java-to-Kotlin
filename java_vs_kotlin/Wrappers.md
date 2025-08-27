# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🎁 Wrappers and Value Classes

### Primitive Wrappers

**Java:**

```java
public class PrimitiveWrappers {
    // Autoboxing from int to Integer
    Integer boxed = 42;

    // Unboxing from Integer to int
    int primitive = boxed;

    // Wrapper utility methods
    public void demonstrateWrappers() {
        Integer.parseInt("42");
        Double.parseDouble("3.14");

        // Wrapper constants
        int maxInt = Integer.MAX_VALUE;
        double minDouble = Double.MIN_VALUE;

        // Null handling required
        Integer nullable = null;
        int value = nullable != null ? nullable : 0;
    }
}
```

**Kotlin:**

```kotlin
class PrimitiveWrappers {
    // Type inference handles boxing
    val boxed: Int? = 42

    // Platform types from Java are handled safely
    val primitive: Int = boxed ?: 0

    fun demonstrateWrappers() {
        // String extension functions
        "42".toInt()
        "3.14".toDouble()

        // Constants are properties
        val maxInt = Int.MAX_VALUE
        val minDouble = Double.MIN_VALUE

        // Null safety with Elvis operator
        val nullable: Int? = null
        val value: Int = nullable ?: 0
    }
}
```

### Value Classes (Inline Classes)

**Java:**

```java
// Java 15+ Record (closest equivalent)
public record EmailAddress(String value) {
    public EmailAddress {
        if (!value.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
    }
}

// Traditional wrapper
public class Money {
    private final BigDecimal amount;
    private final Currency currency;

    public Money(BigDecimal amount, Currency currency) {
        this.amount = amount;
        this.currency = currency;
    }

    // Methods and operators must be explicitly defined
    public Money add(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("Currency mismatch");
        }
        return new Money(amount.add(other.amount), currency);
    }
}
```

**Kotlin:**

```kotlin
// Zero-overhead wrapper
@JvmInline
value class EmailAddress(val value: String) {
    init {
        require(value.contains("@")) { "Invalid email" }
    }

    // Extension properties and functions
    val domain get() = value.substringAfter("@")
    fun isValid() = value.count { it == '@' } == 1
}

// Value class with multiple properties
@JvmInline
value class Money(private val valueInCents: Long) {
    val dollars: Double get() = valueInCents / 100.0

    operator fun plus(other: Money) = Money(valueInCents + other.valueInCents)
    operator fun minus(other: Money) = Money(valueInCents - other.valueInCents)

    companion object {
        fun fromDollars(amount: Double): Money =
            Money((amount * 100).roundToLong())
    }
}
```

### Type-Safe Wrappers

**Java:**

```java
public class TypeSafeWrappers {
    // Using generics for type safety
    public class Box<T> {
        private final T value;

        public Box(T value) {
            this.value = value;
        }

        public T getValue() {
            return value;
        }

        public <R> Box<R> map(Function<T, R> mapper) {
            return new Box<>(mapper.apply(value));
        }
    }

    // Usage
    Box<String> stringBox = new Box<>("Hello");
    Box<Integer> intBox = stringBox.map(String::length);
}
```

**Kotlin:**

```kotlin
class TypeSafeWrappers {
    // Generic wrapper with delegation
    class Box<T>(private val value: T) {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T = value

        fun <R> map(transform: (T) -> R): Box<R> = Box(transform(value))
    }

    // Property delegation
    class User {
        private var nameBox by Box("John")
        var name: String by Delegates.observable("") { _, old, new ->
            println("Name changed from $old to $new")
        }
    }

    // Type-safe builders
    class HtmlBuilder {
        fun div(init: DivBuilder.() -> Unit) =
            DivBuilder().apply(init).build()
    }
}
```

### Memory Optimizations

**Java:**

```java
public class OptimizedWrappers {
    // Flyweight pattern for caching
    private static final Map<String, Wrapper> cache =
        new ConcurrentHashMap<>();

    public static Wrapper valueOf(String value) {
        return cache.computeIfAbsent(value, Wrapper::new);
    }

    // Object pooling
    private static final ObjectPool<ExpensiveWrapper> pool =
        new ObjectPool<>(ExpensiveWrapper::new);
}
```

**Kotlin:**

```kotlin
object OptimizedWrappers {
    // Value class avoids allocation
    @JvmInline
    value class Identifier(val id: String)

    // Object pooling with sequence
    private val pool = sequence {
        while (true) yield(ExpensiveWrapper())
    }.iterator()

    // Reified type parameters
    inline fun <reified T> wrap(value: T): Wrapper<T> =
        when (T::class) {
            String::class -> StringWrapper(value as String)
            Int::class -> IntWrapper(value as Int)
            else -> GenericWrapper(value)
        } as Wrapper<T>
}
```

**Key Differences:**

1. **Value Classes:**

   - Kotlin provides zero-overhead wrappers
   - Java requires full class allocation
   - Kotlin inline classes are optimized at compile time
   - Better type safety without runtime overhead

2. **Primitive Handling:**

   - Kotlin handles boxing automatically
   - Java requires explicit unboxing
   - Kotlin's null safety is built-in
   - Extension functions for primitives

3. **Type Safety:**

   - Kotlin's delegate properties
   - Kotlin's DSL capabilities
   - Reified type parameters
   - Smart casts for type checking

4. **Memory Efficiency:**

   - Kotlin value classes avoid allocation
   - Both support object pooling
   - Kotlin's inline functions
   - Kotlin's delegation is zero-cost

5. **Syntax and Usability:**
   - Kotlin operators are first-class
   - Extension functions add functionality
   - Property delegation for behavior
   - Type-safe builders in Kotlin
