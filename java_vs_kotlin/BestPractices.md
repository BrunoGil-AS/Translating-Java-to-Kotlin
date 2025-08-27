# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🎯 Best Practices

### Naming Conventions

**Java:**

```java
public class UserServiceImpl implements UserService {
    private static final String DEFAULT_ROLE = "USER";
    private final UserRepository userRepository;

    @Autowired
    public UserServiceImpl(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

**Kotlin:**

```kotlin
class UserService(
    private val userRepository: UserRepository
) {
    companion object {
        const val DEFAULT_ROLE = "USER"
    }
}
```

**Key Differences:**

- Kotlin discourages the `Impl` suffix for implementations
- Use `companion object` instead of `static final` fields
- Properties are preferred over fields
- Constructor injection is more concise

### Code Organization

**Java:**

```java
public class OrderProcessor {
    private void validateOrder(Order order) { /*...*/ }
    private BigDecimal calculateTotal(Order order) { /*...*/ }

    public Order processOrder(Order order) {
        validateOrder(order);
        BigDecimal total = calculateTotal(order);
        return order;
    }
}
```

**Kotlin:**

```kotlin
class OrderProcessor {
    fun processOrder(order: Order): Order {
        order.validate()
        order.calculateTotal()
        return order
    }
}

private fun Order.validate() { /*...*/ }
private fun Order.calculateTotal(): BigDecimal { /*...*/ }
```

**Key Differences:**

- Kotlin encourages extension functions for better organization
- Properties and functions are public by default
- Use extension functions instead of utility classes
- Prefer expression bodies for simple functions

### Null Safety Best Practices

**Java:**

```java
public class NullHandling {
    public String processName(String name) {
        Objects.requireNonNull(name, "Name cannot be null");
        return name.trim().toUpperCase();
    }

    public Optional<User> findUser(String id) {
        return Optional.ofNullable(userRepository.findById(id));
    }
}
```

**Kotlin:**

```kotlin
class NullHandling {
    fun processName(name: String): String {
        return name.trim().uppercase()
    }

    fun findUser(id: String): User? {
        return userRepository.findById(id)
    }
}
```

**Key Differences:**

- Use non-nullable types by default
- Leverage the type system instead of runtime checks
- Prefer `?` over `Optional`
- Use `requireNotNull()` or `checkNotNull()` for explicit validation

### Performance Considerations

**Java:**

```java
public class CollectionProcessing {
    public List<String> processItems(List<String> items) {
        return items.stream()
                   .filter(item -> item != null)
                   .map(String::trim)
                   .collect(Collectors.toList());
    }
}
```

**Kotlin:**

```kotlin
class CollectionProcessing {
    fun processItems(items: List<String>): List<String> {
        return items.asSequence()
                   .filterNotNull()
                   .map { it.trim() }
                   .toList()
    }
}
```

**Key Differences:**

- Use `Sequence` for large collection processing
- Leverage built-in functions like `filterNotNull()`
- Take advantage of inline functions for better performance
- Use `Array<T>` for primitive arrays to avoid boxing

### Testing Best Practices

**Java:**

```java
@Test
public void shouldProcessOrder() {
    // Given
    Order order = new Order();
    order.setItems(Arrays.asList(new Item("product", 10.0)));

    // When
    Order result = orderProcessor.process(order);

    // Then
    assertThat(result.getTotal()).isEqualTo(10.0);
}
```

**Kotlin:**

```kotlin
@Test
fun `should process order`() {
    // Given
    val order = Order(
        items = listOf(Item("product", 10.0))
    )

    // When
    val result = orderProcessor.process(order)

    // Then
    assertThat(result.total).isEqualTo(10.0)
}
```

**Key Differences:**

- Use backticks for readable test names
- Leverage data classes for test data
- Take advantage of named parameters
- Use property access syntax
