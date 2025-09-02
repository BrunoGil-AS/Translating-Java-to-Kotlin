# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🛠️ Utility Classes vs Extension Functions

Utility functions in Java are typically grouped in **final classes with static methods**, whereas Kotlin provides **extension functions** that allow adding methods to existing classes without modifying them.

### Traditional Java Utilities

- Java uses **static utility classes**.
- Methods are called via the class name or static import.

```java
public final class StringUtils {
    private StringUtils() {} // Prevent instantiation

    public static String truncate(String text, int maxLength) {
        if (text == null || text.length() <= maxLength) return text;
        return text.substring(0, maxLength) + "...";
    }

    public static boolean isValidEmail(String email) {
        return email != null && email.matches("^[A-Za-z0-9+_.-]+@(.+)$");
    }
}

// Usage:
String result = StringUtils.truncate("Hello World", 5);
```

**Limitations:**

- Static context only, cannot access instance members.
- Requires explicit class reference.
- Less discoverable in IDEs.

---

### Kotlin Extension Functions

- Kotlin allows **extending existing classes with new functions**.
- Extensions use the **receiver object (`this`)**.

```kotlin
fun String.truncate(maxLength: Int): String =
    if (length <= maxLength) this else take(maxLength) + "..."

fun String.isValidEmail(): Boolean =
    matches(Regex("^[A-Za-z0-9+_.-]+@(.+)$"))

// Usage
val result = "Hello World".truncate(5)
val valid = "test@email.com".isValidEmail()
```

**Key Advantages:**

- Methods appear as **natural instance methods**.
- No need for utility classes.
- Better IDE discoverability.
- Can extend nullable types.

---

### Using Kotlin Extensions from Java

```java
String truncated = StringExtensionsKt.truncate("Hello", 3);
boolean isValid = StringExtensionsKt.isValidEmail("test@example.com");

List<String> list = Arrays.asList("1", "2", "3");
List<Integer> numbers = CollectionsKt.map(list, str -> Integer.parseInt(str));
```

- Kotlin extensions **compile to static methods**.
- Java can call them using the generated class name (`StringExtensionsKt`).

---

### Extension Properties

```kotlin
val String.emailDomain: String?
    get() = split("@").getOrNull(1)

class ValidationUtils {
    companion object {
        private val emailRegex = Regex("^[A-Za-z0-9+_.-]+@(.+)$")
    }
}

val String.isEmail: Boolean
    get() = matches(ValidationUtils.emailRegex)
```

- Provides **computed properties**.
- Can use companion objects for shared constants.

---

### Advanced Extension Functions

```kotlin
fun <T> T.applyIf(condition: Boolean, block: T.() -> T): T = if (condition) block() else this
fun StringBuilder.appendIfNotEmpty(text: String) {
    if (isNotEmpty()) append(", ")
    append(text)
}
infix fun <T> List<T>.intersectWith(other: List<T>): List<T> = filter { it in other }
operator fun Int.times(str: String): String = str.repeat(this)
```

- Supports **generics, receiver types, infix notation, operator overloading**.

---

### Best Practices & Tips

1. **Design Philosophy:**

   - Java: Static utility methods.
   - Kotlin: Extensions on types, enabling natural method chaining.

2. **Interoperability:**

   - Extensions compile to static methods.
   - Use `@JvmName`, `@JvmStatic`, and `@JvmOverloads` for Java interop.

3. **Scoping:**

   - Extensions can be **local, private, or receiver-specific**.

4. **Performance:**

   - No runtime overhead, resolved at compile-time.
   - Can use `inline` functions for optimization.

5. **Use Cases:**

   - Enhance existing classes.
   - Build DSLs.
   - Add utility methods with **type safety**.

6. **Limitations:**

   - Cannot override class members.
   - Cannot store state.
   - Extension properties cannot have backing fields.
   - Potential for **resolution ambiguity** if multiple extensions match.
