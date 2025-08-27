# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 📝 String Methods

### Basic String Operations

**Java:**

```java
public class StringUtils {
    public String capitalize(String text) {
        if (text == null || text.isEmpty()) {
            return text;
        }
        return text.substring(0, 1).toUpperCase() + text.substring(1);
    }

    public boolean isNullOrEmpty(String text) {
        return text == null || text.trim().isEmpty();
    }
}
```

**Kotlin:**

```kotlin
// Extension functions instead of utility class
fun String.capitalizeFirst() = this.replaceFirstChar { it.uppercase() }

// Built-in null handling
fun String?.isNullOrEmpty(): Boolean = this == null || this.isEmpty()
fun String?.isNullOrBlank(): Boolean = this == null || this.isBlank()
```

### String Templates and Interpolation

**Java:**

```java
public class MessageFormatter {
    public String format(String name, int age) {
        return String.format("Name: %s, Age: %d", name, age);
    }

    public String buildQuery(String table, List<String> columns) {
        return "SELECT " + String.join(", ", columns) +
               " FROM " + table;
    }
}
```

**Kotlin:**

```kotlin
class MessageFormatter {
    fun format(name: String, age: Int) = "Name: $name, Age: $age"

    fun buildQuery(table: String, columns: List<String>) =
        "SELECT ${columns.joinToString(", ")} FROM $table"

    fun complexTemplate(user: User) = """
        User Details:
        ID: ${user.id}
        Name: ${user.name}
        Email: ${user.email}
        """.trimIndent()
}
```

### String Processing and Transformation

**Java:**

```java
public class StringProcessor {
    public List<String> splitAndTrim(String text, String delimiter) {
        if (text == null) return Collections.emptyList();
        return Arrays.stream(text.split(delimiter))
                    .map(String::trim)
                    .filter(s -> !s.isEmpty())
                    .collect(Collectors.toList());
    }

    public String repeat(String text, int times) {
        return text.repeat(times); // Java 11+
    }
}
```

**Kotlin:**

```kotlin
class StringProcessor {
    fun splitAndTrim(text: String?, delimiter: String) =
        text?.split(delimiter)
            ?.map { it.trim() }
            ?.filter { it.isNotEmpty() }
            ?: emptyList()

    // Built-in repeat function
    fun repeat(text: String, times: Int) = text.repeat(times)

    // Extension function for common transformations
    fun String.toSlug() = lowercase()
        .replace(Regex("[^a-z0-9\\s-]"), "")
        .replace(Regex("\\s+"), "-")
}
```

### Advanced String Features

**Java:**

```java
public class AdvancedStringOps {
    public String truncate(String text, int maxLength) {
        if (text == null) return null;
        if (text.length() <= maxLength) return text;
        return text.substring(0, maxLength) + "...";
    }

    public String normalize(String text) {
        return text.toLowerCase()
                  .replaceAll("\\s+", " ")
                  .trim();
    }
}
```

**Kotlin:**

```kotlin
class AdvancedStringOps {
    fun truncate(text: String, maxLength: Int) =
        text.takeIf { it.length <= maxLength }
            ?: "${text.take(maxLength)}..."

    fun normalize(text: String) = text.lowercase()
        .replace("\\s+".toRegex(), " ")
        .trim()

    // Multiline string with margin prefix
    fun generateHtml(user: User) = """
        |<user>
        |  <name>${user.name}</name>
        |  <email>${user.email}</email>
        |</user>
        """.trimMargin()
}
```

**Key Differences:**

- Kotlin provides string templates with `$` and `${}`
- Raw strings (`"""`) for multiline text without escaping
- Built-in null safety with safe calls (`?.`) and Elvis operator (`?:`)
- Extension functions make string operations more readable
- More functional approach with chained operations
- Built-in `trimMargin()` for structured text
- Smart casts reduce need for null checks
