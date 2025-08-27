# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🛡️ Optional vs Null Safety

### Handling Nullable Values

**Java:**

```java
// Java's approach to null safety using Optional
public class UserService {
    // Wrapping nullable result in Optional
    public Optional<User> findById(Long id) {
        User user = repository.findById(id);
        return Optional.ofNullable(user);
    }

    // Chaining Optional operations
    public String getUppercaseUsername(Long id) {
        return findById(id)
                .map(User::getUsername)     // Transform if present
                .map(String::toUpperCase)   // Chain transformations
                .orElse("UNKNOWN");         // Provide default value
    }

    // Conditional execution with Optional
    public void processUser(User user) {
        Optional.ofNullable(user)          // Wrap nullable value
                .ifPresent(this::sendWelcomeEmail); // Execute if present
    }

    // Multiple fallback example
    public String getUserDisplayName(Long id) {
        return findById(id)
                .map(User::getDisplayName)
                .or(() -> findById(id).map(User::getEmail))
                .orElse("Anonymous");
    }
}
```

**Kotlin:**

```kotlin
// Kotlin's native null safety features
class UserService {
    // Return type explicitly marks nullability
    fun findById(id: Long): User? = repository.findById(id)

    // Safe call operator with Elvis operator
    fun getUppercaseUsername(id: Long): String =
        findById(id)?.username?.uppercase() ?: "UNKNOWN"

    // Safe execution with let
    fun processUser(user: User?) {
        user?.let { sendWelcomeEmail(it) }
    }

    // Multiple fallback with Elvis operator chain
    fun getUserDisplayName(id: Long): String =
        findById(id)?.displayName
            ?: findById(id)?.email
            ?: "Anonymous"

    // Combining null checks with conditions
    fun processVerifiedUser(user: User?) {
        user?.takeIf { it.isVerified }
            ?.let { sendWelcomeEmail(it) }
    }
}
```

**Key Differences:**

- Kotlin uses type system for null safety
- Safe call operator (?.) for chaining
- Elvis operator (?:) for defaults
- No wrapper object overhead
- Smart casts after null checks

### Safe Property Access

**Java:**

```java
public class AddressService {
    public String getCountry(User user) {
        return Optional.ofNullable(user)
                .map(User::getAddress)
                .map(Address::getCountry)
                .map(Country::getName)
                .orElse("Unknown");
    }

    public void updateIfPresent(User user) {
        Optional.ofNullable(user)
                .filter(u -> u.getAge() > 18)
                .ifPresent(this::processAdult);
    }
}
```

**Kotlin:**

```kotlin
class AddressService {
    fun getCountry(user: User?): String =
        user?.address?.country?.name ?: "Unknown"

    fun updateIfPresent(user: User?) {
        user?.takeIf { it.age > 18 }
            ?.let { processAdult(it) }
    }
}
```

### Collection Nullability

**Java:**

```java
public class CollectionProcessor {
    public List<String> processItems(List<String> items) {
        return Optional.ofNullable(items)
                .map(list -> list.stream()
                        .filter(Objects::nonNull)
                        .map(String::trim)
                        .filter(s -> !s.isEmpty())
                        .collect(Collectors.toList()))
                .orElse(Collections.emptyList());
    }
}
```

**Kotlin:**

```kotlin
class CollectionProcessor {
    fun processItems(items: List<String>?): List<String> =
        items?.filterNotNull()
            ?.map { it.trim() }
            ?.filter { it.isNotEmpty() }
            ?: emptyList()

    // Type-safe distinction between nullable list and list of nullables
    val nullableList: List<String>? = null // the list itself can be null
    val listOfNullables: List<String?> = listOf("a", null, "b") // elements can be null
}
```

### Platform Types and Interop

**Java:**

```java
public interface UserRepository {
    @Nullable // JetBrains annotation
    User findByEmail(String email);

    @NotNull // JetBrains annotation
    List<User> findAllActive();
}
```

**Kotlin:**

```kotlin
interface UserRepository {
    // Platform type from Java is treated as nullable in Kotlin
    fun findByEmail(email: String): User?

    // NotNull annotation is respected
    fun findAllActive(): List<User>
}

// Extension function to handle Java APIs safely
fun <T> T?.toNullable(): T? = this
fun <T: Any> T?.requireNonNull(): T = requireNotNull(this)
```

**Key Differences:**

- Kotlin uses type system for null safety (no `Optional`)
- Safe call operator (`?.`) chains nullable calls
- Elvis operator (`?:`) provides default values
- Platform types from Java are handled specially
- Smart casts reduce explicit null checks
- Built-in null-safe collection operations
- Extension functions for safer Java interop
- No wrapper object overhead like `Optional`
- Compile-time null safety vs runtime checks
