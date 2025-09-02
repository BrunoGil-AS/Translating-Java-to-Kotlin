# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧪 Extension Functions

**_What are the extension functions?_**

Extension functions are a powerful feature in Kotlin that allows you to add new functionality to existing classes without modifying their source code. They are defined outside the class they extend and can be called as if they were member functions of the class.

### Static Methods vs Extension Functions

Usually, when it comes to add extra functionality to existing classes in Java, developers create static utility classes, adding static methods for the desired functionality.

**Java (Static Utility Methods):**

```java
// Traditional Java utility class approach
public class StringUtils {
    // Static method for email validation
    public static boolean isValidEmail(String email) {
        if (email == null || email.trim().isEmpty()) {
            return false;
        }
        return email.contains("@") && email.contains(".");
    }

    // Static utility for string truncation
    public static String truncate(String text, int maxLength) {
        if (text == null) return null;
        if (text.length() <= maxLength) return text;
        return text.substring(0, maxLength) + "...";
    }

    // Static utility for whitespace removal
    public static String removeWhitespace(String text) {
        return text == null ? null : text.replaceAll("\s+", "");
    }
}

// Usage requires static imports or class reference
public class EmailService {
    public void sendEmail(String email, String message) {
        // Methods called through utility class
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
// Extension functions extend existing types
// Nullable string extension
fun String?.isValidEmail(): Boolean {
    return this != null && this.contains("@") && this.contains(".")
}

// Regular string extension
fun String.truncate(maxLength: Int): String {
    return if (this.length <= maxLength) this
           else this.take(maxLength) + "..."
}

// Single-expression function extension
fun String.removeWhitespace(): String =
    this.replace(Regex("\s+"), "")

// Usage demonstrates natural method chaining
class EmailService {
    fun sendEmail(email: String?, message: String) {
        if (email.isValidEmail()) {  // Call directly on nullable string
            val processedMessage = message
                .truncate(100)       // Chain multiple extensions
                .removeWhitespace()  // Natural method syntax
            // Send email...
        }
    }
}
```

**Key Differences:**

- Kotlin extensions appear as regular methods
- Support for nullable type extensions
- Natural method chaining capability
- No utility class needed
- Extension functions are resolved statically

### Usage in Collections and Utilities

For common collection operations, extension functions can greatly simplify the code.

**Java:**

```java
// Java collection utility methods require separate class
public class CollectionUtils {
    // Generic method to filter null values
    public static <T> List<T> filterNotNull(List<T> list) {
        return list.stream()
            .filter(Objects::nonNull)
            .collect(Collectors.toList());
    }

    // Generic finder with predicate
    public static <T> Optional<T> findFirst(List<T> list, Predicate<T> predicate) {
        return list.stream()
            .filter(predicate)
            .findFirst();
    }

    // Transform and filter nulls
    public static <T, R> List<R> mapNotNull(List<T> list, Function<T, R> mapper) {
        return list.stream()
            .map(mapper)
            .filter(Objects::nonNull)
            .collect(Collectors.toList());
    }
}

// Usage requires explicit utility class calls
public class OrderService {
    public List<String> getValidOrderNumbers(List<Order> orders) {
        return CollectionUtils.mapNotNull(
            CollectionUtils.filterNotNull(orders),
            order -> order.getOrderNumber().length() > 5 ?
                     order.getOrderNumber() : null
        );
    }
}
```

**Kotlin (Extensions + Built-in Functions):**

```kotlin
// Custom extension functions enhance existing types
// Extension for lists of nullable CharSequences
fun <T> List<T?>.filterNotNullAndNotEmpty(): List<T>
        where T : CharSequence {
    return this.filterNotNull().filter { it.isNotEmpty() }
}

// Extension with custom error handling
fun <T> List<T>.findFirstOrThrow(
    predicate: (T) -> Boolean,
    errorMessage: String
): T {
    return this.firstOrNull(predicate)
           ?: throw NoSuchElementException(errorMessage)
}

// Usage demonstrates fluent API style
class OrderService {
    fun getValidOrderNumbers(orders: List<Order?>): List<String> {
        return orders
            .filterNotNull()           // Built-in extension
            .map { it.orderNumber }    // Transform to strings
            .filterNotNullAndNotEmpty() // Custom extension
            .filter { it.length > 5 }  // Chain operations
    }

    fun findLargeOrder(orders: List<Order>): Order {
        return orders.findFirstOrThrow(
            { it.amount > 1000.toBigDecimal() },
            "No large orders found"
        )
    }
}
```

**Key Differences:**

- Kotlin enables custom extension functions
- Built-in support for method chaining
- More natural null handling
- Extension functions can use generics
- Cleaner error handling with Elvis operator

**Extension Function Advantages:**

- More natural and fluent syntax (method chaining)
- No need for utility class imports
- Better IDE integration (contextual autocomplete)
- Direct support for nullable types

**Java Static Method Advantages:**

- More explicit about functionality origin
- Familiar to Java developers
- Better control over imports and namespaces

### Limitations

- Extension functions are resolved statically, meaning they do not actually modify the classes they extend.
- They can be particularly useful for adding utility functions to existing libraries without needing to subclass or modify the original classes.
- Extension functions can be generic, allowing for flexible and reusable code.
- Extension functions defined in **Kotlin files** can be imported and used in other files, promoting code reuse.
  - You usually define them in a `.kt` file (often named something like `StringExtensions.kt` or `Extensions.kt`).
  - They **belong to the package** where the file is declared.
  - You can **import them into other files** just like any other function.

### Example

```kotlin
package com.example.utils

// Extension function on String
fun String.isEmailValid(): Boolean {
    return this.contains("@") && this.contains(".")
}
```

```kotlin
package com.example.main

import com.example.utils.isEmailValid  // Import the extension

fun main() {
    val email = "test@example.com"
    println(email.isEmailValid()) // ✅ Works
}
```

### Key Notes

- If you **import the whole package** (`import com.example.utils.*`), all extension functions in that package are available.
- They don’t actually modify the class; at compile time, they’re just **static functions** with a receiver as the first argument.
- If there are **conflicting extension functions** with the same name/signature, the one from the **closest scope (imported last or local)** is used.
