# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧵 Control Flow

### Conditionals

**Java:**

```java
public class OrderProcessor {
    public String processOrder(OrderStatus status, double amount) {
        // Traditional if-else
        if (status == OrderStatus.PENDING) {
            return "Processing order for $" + amount;
        } else if (status == OrderStatus.CONFIRMED) {
            return "Order confirmed for $" + amount;
        } else if (status == OrderStatus.CANCELLED) {
            return "Order cancelled";
        } else {
            return "Unknown status";
        }
    }

    // Switch expression (Java 14+)
    public double calculateDiscount(CustomerType type) {
        return switch (type) {
            case PREMIUM -> 0.15;
            case REGULAR -> 0.05;
            case NEW -> 0.10;
            default -> 0.0;
        };
    }
}
```

**Kotlin:**

```kotlin
class OrderProcessor {
    fun processOrder(status: OrderStatus, amount: Double): String {
        // when as expression (more powerful than switch)
        return when (status) {
            OrderStatus.PENDING -> "Processing order for $$amount"
            OrderStatus.CONFIRMED -> "Order confirmed for $$amount"
            OrderStatus.CANCELLED -> "Order cancelled"
            else -> "Unknown status"
        }
    }

    fun calculateDiscount(type: CustomerType): Double = when (type) {
        CustomerType.PREMIUM -> 0.15
        CustomerType.REGULAR -> 0.05
        CustomerType.NEW -> 0.10
    } // No 'else' needed if all cases are covered

    // when with complex conditions
    fun categorizeOrder(amount: Double, items: Int): String = when {
        amount > 1000 && items > 10 -> "Bulk order"
        amount > 500 -> "Large order"
        items > 20 -> "Many items"
        else -> "Regular order"
    }
}
```

**Key Differences:**

- Kotlin's `when` is more expressive than Java's `switch`
- Kotlin allows complex conditions in `when` without specific values
- `when` can be used as an expression, not just as a statement
- Kotlin doesn't require `break` statements

### Loops

**Java:**

```java
public class CollectionProcessor {
    public void processItems(List<String> items) {
        // Traditional for loop with index
        for (int i = 0; i < items.size(); i++) {
            System.out.println("Item " + i + ": " + items.get(i));
        }

        // Enhanced for (foreach)
        for (String item : items) {
            System.out.println("Processing: " + item);
        }

        // While loop
        Iterator<String> iterator = items.iterator();
        while (iterator.hasNext()) {
            String item = iterator.next();
            if (item.startsWith("skip")) {
                continue;
            }
            System.out.println(item);
        }
    }
}
```

**Kotlin:**

```kotlin
class CollectionProcessor {
    fun processItems(items: List<String>) {
        // For loop with indices
        for (i in items.indices) {
            println("Item $i: ${items[i]}")
        }

        // For loop with index and value
        for ((index, item) in items.withIndex()) {
            println("Item $index: $item")
        }

        // Simple foreach
        for (item in items) {
            println("Processing: $item")
        }

        // Functional with forEach
        items.forEach { item ->
            if (!item.startsWith("skip")) {
                println(item)
            }
        }

        // Ranges
        for (i in 1..10) {
            println("Number: $i")
        }

        for (i in 10 downTo 1 step 2) {
            println("Countdown: $i")
        }
    }
}
```

**Key Differences:**

- Kotlin offers multiple idiomatic ways to iterate
- Ranges (`1..10`, `10 downTo 1`) are more expressive
- `withIndex()` eliminates boilerplate for accessing indices
- Better integration between imperative loops and functional programming

### Lambda Expressions and Anonymous Functions

**Java:**

```java
public class FunctionalProcessor {
    public List<String> processNames(List<User> users) {
        return users.stream()
            .filter(user -> user.getAge() >= 18) // Simple lambda
            .map(user -> user.getName().toUpperCase())
            .filter(name -> name.startsWith("A"))
            .collect(Collectors.toList());
    }

    // Higher-order function
    public void processWithCallback(List<String> items, Consumer<String> processor) {
        items.forEach(processor);
    }

    public void usage() {
        List<String> items = Arrays.asList("apple", "banana", "cherry");

        // Multi-line lambda
        processWithCallback(items, item -> {
            String processed = item.toUpperCase();
            System.out.println("Processing: " + processed);
            // More logic here
        });
    }
}
```

**Kotlin:**

```kotlin
class FunctionalProcessor {
    fun processNames(users: List<User>): List<String> {
        return users
            .filter { it.age >= 18 } // 'it' is the implicit parameter
            .map { it.name.uppercase() }
            .filter { it.startsWith("A") }
    }

    // Higher-order function
    fun processWithCallback(items: List<String>, processor: (String) -> Unit) {
        items.forEach(processor)
    }

    fun usage() {
        val items = listOf("apple", "banana", "cherry")

        // Multi-line lambda
        processWithCallback(items) { item ->
            val processed = item.uppercase()
            println("Processing: $processed")
            // More logic here
        }

        // If lambda is the last parameter, it can go outside parentheses
        items.forEach { item ->
            println("Item: $item")
        }
    }

    // Anonymous function with explicit type
    val validator: (String) -> Boolean = fun(input: String): Boolean {
        return input.isNotEmpty() && input.length > 3
    }
}
```

**Key Differences:**

- Kotlin uses `it` as implicit parameter for simple lambdas
- Cleaner syntax for lambdas that are the last parameter
- No need for `Collectors` for collection operations
- Functions are first-class citizens more naturally
