# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧪 Extension Functions

### Static Methods vs Extension Functions

**Java (Static Utility Methods):**

```java
public class StringUtils {
    public static boolean isValidEmail(String email) {
        if (email == null || email.trim().isEmpty()) {
            return false;
        }
        return email.contains("@") && email.contains(".");
    }

    public static String truncate(String text, int maxLength) {
        if (text == null) return null;
        if (text.length() <= maxLength) return text;
        return text.substring(0, maxLength) + "...";
    }

    public static String removeWhitespace(String text) {
        return text == null ? null : text.replaceAll("\s+", "");
    }
}

// Usage
public class EmailService {
    public void sendEmail(String email, String message) {
        if (StringUtils.isValidEmail(email)) {
            String truncatedMessage = StringUtils.truncate(message, 100);
            String cleanMessage = StringUtils.removeWhitespace(truncatedMessage);
            // Send email...
        }
    }
}
```

**Kotlin (Extension Functions):**

```kotlin
// Define extensions for String
fun String?.isValidEmail(): Boolean {
    return this != null && this.contains("@") && this.contains(".")
}

fun String.truncate(maxLength: Int): String {
    return if (this.length <= maxLength) this else this.take(maxLength) + "..."
}

fun String.removeWhitespace(): String = this.replace(Regex("\s+"), "")

// Usage - more natural syntax
class EmailService {
    fun sendEmail(email: String?, message: String) {
        if (email.isValidEmail()) {
            val processedMessage = message
                .truncate(100)
                .removeWhitespace()
            // Send email...
        }
    }
}
```

### Usage in Collections and Utilities

**Java:**

```java
public class CollectionUtils {
    public static <T> List<T> filterNotNull(List<T> list) {
        return list.stream()
            .filter(Objects::nonNull)
            .collect(Collectors.toList());
    }

    public static <T> Optional<T> findFirst(List<T> list, Predicate<T> predicate) {
        return list.stream()
            .filter(predicate)
            .findFirst();
    }

    public static <T, R> List<R> mapNotNull(List<T> list, Function<T, R> mapper) {
        return list.stream()
            .map(mapper)
            .filter(Objects::nonNull)
            .collect(Collectors.toList());
    }
}

// Usage
public class OrderService {
    public List<String> getValidOrderNumbers(List<Order> orders) {
        return CollectionUtils.mapNotNull(
            CollectionUtils.filterNotNull(orders),
            order -> order.getOrderNumber().length() > 5 ? order.getOrderNumber() : null
        );
    }
}
```

**Kotlin (Extensions + Built-in Functions):**

```kotlin
// Custom extensions for specific cases
fun <T> List<T?>.filterNotNullAndNotEmpty(): List<T> where T : CharSequence {
    return this.filterNotNull().filter { it.isNotEmpty() }
}

fun <T> List<T>.findFirstOrThrow(predicate: (T) -> Boolean, errorMessage: String): T {
    return this.firstOrNull(predicate) ?: throw NoSuchElementException(errorMessage)
}

// Usage - more fluent and expressive
class OrderService {
    fun getValidOrderNumbers(orders: List<Order?>): List<String> {
        return orders
            .filterNotNull()
            .map { it.orderNumber }
            .filterNotNullAndNotEmpty()
            .filter { it.length > 5 }
    }

    fun findLargeOrder(orders: List<Order>): Order {
        return orders.findFirstOrThrow(
            { it.amount > 1000.toBigDecimal() },
            "No large orders found"
        )
    }
}
```

**Extension Function Advantages:**

- More natural and fluent syntax (method chaining)
- No need for utility class imports
- Better IDE integration (contextual autocomplete)
- Direct support for nullable types

**Java Static Method Advantages:**

- More explicit about functionality origin
- Familiar to Java developers
- Better control over imports and namespaces
