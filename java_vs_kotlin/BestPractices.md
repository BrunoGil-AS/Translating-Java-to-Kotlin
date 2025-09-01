# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

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

**Comparison Table:**

| Aspect                | Java                          | Kotlin                       |
| --------------------- | ----------------------------- | ---------------------------- |
| Implementation naming | Often uses `Impl` suffix      | Discourages `Impl` suffix    |
| Constants             | `static final` fields         | `companion object` constants |
| State                 | Fields                        | Properties                   |
| Dependency injection  | Constructor with `@Autowired` | Primary constructor          |

---

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

**Comparison Table:**

| Aspect         | Java                            | Kotlin                        |
| -------------- | ------------------------------- | ----------------------------- |
| Helpers        | Private methods inside class    | Extension functions           |
| Utilities      | Utility classes common          | Extension functions preferred |
| Defaults       | Members explicit public/private | Public by default             |
| Function style | Block bodies                    | Expression bodies possible    |

---

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

**Comparison Table:**

| Aspect          | Java                       | Kotlin                           |
| --------------- | -------------------------- | -------------------------------- |
| Null checks     | `Objects.requireNonNull()` | Type system + `requireNotNull()` |
| Optionality     | `Optional<T>`              | Nullable types (`T?`)            |
| Enforcement     | Runtime checks common      | Compiler enforced                |
| Safety defaults | Everything nullable        | Non-nullable by default          |

---

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

**Comparison Table:**

| Aspect             | Java                            | Kotlin                          |
| ------------------ | ------------------------------- | ------------------------------- |
| Collections        | Streams API                     | `Sequence`                      |
| Null filtering     | Explicit `filter(item != null)` | `filterNotNull()`               |
| Performance tuning | Streams parallelization         | Inline + sequences              |
| Primitive arrays   | `int[]` vs boxing with generics | `Array<T>` and primitive arrays |

---

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

**Comparison Table:**

| Aspect      | Java                   | Kotlin                       |
| ----------- | ---------------------- | ---------------------------- |
| Test names  | Method names           | Backtick strings             |
| Test data   | Manual object creation | Data classes with named args |
| Assertions  | Getter-based           | Property access syntax       |
| Readability | More boilerplate       | Concise                      |

---
