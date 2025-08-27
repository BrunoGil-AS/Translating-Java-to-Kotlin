# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🛠️ Utility Classes vs Extension Functions

### Traditional Java Utilities

**Java:**

```java
public final class StringUtils {
    private StringUtils() {} // Prevent instantiation

    public static String truncate(String text, int maxLength) {
        if (text == null || text.length() <= maxLength) {
            return text;
        }
        return text.substring(0, maxLength) + "...";
    }

    public static boolean isValidEmail(String email) {
        return email != null &&
               email.matches("^[A-Za-z0-9+_.-]+@(.+)$");
    }
}

// Usage in Java
String result = StringUtils.truncate("Hello World", 5);
```

### Kotlin Extension Functions

```kotlin
// File: StringExtensions.kt
fun String.truncate(maxLength: Int): String =
    if (length <= maxLength) this
    else take(maxLength) + "..."

fun String.isValidEmail(): Boolean =
    matches(Regex("^[A-Za-z0-9+_.-]+@(.+)$"))

// Usage in Kotlin
val result = "Hello World".truncate(5)
```

### Using Kotlin Extensions from Java

```java
public class JavaConsumer {
    public void useKotlinExtensions() {
        // Static method style invocation
        String truncated = StringExtensionsKt.truncate("Hello", 3);
        boolean isValid = StringExtensionsKt.isValidEmail("test@example.com");

        // Calling extensions on collections
        List<String> list = Arrays.asList("1", "2", "3");
        List<Integer> numbers = CollectionsKt.map(list,
            str -> Integer.parseInt(str));
    }
}
```

### Extension Properties

```kotlin
// Extension property
val String.emailDomain: String?
    get() = this.split("@").getOrNull(1)

// Extension property with backing field in companion object
class ValidationUtils {
    companion object {
        private val emailRegex = Regex("^[A-Za-z0-9+_.-]+@(.+)$")
    }
}

val String.isEmail: Boolean
    get() = matches(ValidationUtils.emailRegex)
```

### Advanced Extension Functions

```kotlin
// Extension functions with generics
fun <T> T.applyIf(condition: Boolean, block: T.() -> T): T =
    if (condition) block() else this

// Extension function with receiver type
fun StringBuilder.appendIfNotEmpty(text: String) {
    if (isNotEmpty()) append(", ")
    append(text)
}

// Infix extension function
infix fun <T> List<T>.intersectWith(other: List<T>): List<T> =
    filter { it in other }

// Operator overloading via extension
operator fun Int.times(str: String): String = str.repeat(this)
```

### Extension Function Best Practices

```kotlin
// Scope control with extensions
class DatabaseConnection {
    // Limited visibility extension
    private fun ResultSet.toUser(): User =
        User(
            id = getLong("id"),
            name = getString("name")
        )

    fun getUser(id: Long): User = connection
        .prepareStatement("SELECT * FROM users WHERE id = ?")
        .use { stmt ->
            stmt.setLong(1, id)
            stmt.executeQuery().use { rs ->
                if (rs.next()) rs.toUser()
                else throw NoSuchElementException()
            }
        }
}

// Extensions for better DSL
class HtmlBuilder {
    fun build(block: StringBuilder.() -> Unit): String =
        StringBuilder().apply(block).toString()
}

// Usage
val html = HtmlBuilder().build {
    append("<div>")
    append("Content")
    append("</div>")
}
```

### Java Interoperability Tips

```kotlin
// @JvmName to customize Java method name
@get:JvmName("domain")
val String.emailDomain: String?
    get() = split("@").getOrNull(1)

// @JvmStatic for companion object methods
class Utils {
    companion object {
        @JvmStatic
        fun helper(input: String): String = input.uppercase()
    }
}

// @JvmOverloads for default parameters
class Processor {
    @JvmOverloads
    fun process(text: String, count: Int = 1) = text.repeat(count)
}
```

**Key Differences & Best Practices:**

1. **Design Philosophy:**

   - Java: Static utility methods in final classes
   - Kotlin: Extension functions on types
   - Kotlin: More natural method chaining
   - Kotlin: Better IDE support for discovery

2. **Interoperability:**

   - Extensions compile to static methods
   - Java can call Kotlin extensions
   - @JvmName controls Java method names
   - @JvmStatic for companion object access

3. **Scoping:**

   - Extensions can be locally scoped
   - Extensions can be private
   - Can extend sealed hierarchies
   - Can be receiver-specific

4. **Performance:**

   - No runtime overhead
   - Compile-time resolution
   - Inline function optimization
   - No object creation needed

5. **Use Cases:**

   - Enhance existing classes
   - Create DSLs
   - Add utility methods
   - Better type safety

6. **Limitations:**
   - Can't override members
   - No state in extensions
   - No extension properties with backing fields
   - Resolution ambiguity possible
