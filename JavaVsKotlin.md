# Complete Java vs Kotlin Backend Comparison

## 🏗️ Basic Structure

### Class Declaration

**Java:**

```java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User findUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
    }
}
```

**Kotlin:**

```kotlin
class UserService(
    private val userRepository: UserRepository
) {
    fun findUserById(id: Long): User {
        return userRepository.findById(id)
            .orElseThrow { UserNotFoundException("User not found: $id") }
    }
}
```

**Key Differences:**

- Kotlin eliminates constructor boilerplate and property declarations
- String interpolation (`$id`) is more natural than Java concatenation
- Kotlin infers access modifiers (`public` by default)

**Kotlin Advantages:** More concise, less repetitive code, more expressive syntax
**Java Advantages:** More explicit, familiar to traditional Java developers

### Methods and Functions

**Java:**

```java
public class MathUtils {
    public static int add(int a, int b) {
        return a + b;
    }

    public static int multiply(int a, int b, int factor) {
        return (a + b) * factor;
    }

    // Method overloading for optional parameters
    public static int multiply(int a, int b) {
        return multiply(a, b, 1);
    }
}
```

**Kotlin:**

```kotlin
class MathUtils {
    companion object {
        fun add(a: Int, b: Int): Int = a + b

        // Function with default parameter and single expression
        fun multiply(a: Int, b: Int, factor: Int = 1): Int = (a + b) * factor
    }
}

// Top-level functions (not necessarily inside classes)
fun add(a: Int, b: Int): Int = a + b
```

**Key Differences:**

- Kotlin supports default parameters, eliminating method overloading
- Single expression functions (`= expression`) reduce boilerplate
- Kotlin allows top-level functions, not necessarily within classes
- `companion object` is equivalent to Java static methods

### Variables

**Java:**

```java
public class ConfigurationService {
    private final String apiUrl; // Immutable after initialization
    private String currentEnvironment; // Mutable

    public ConfigurationService() {
        this.apiUrl = "https://api.example.com";
        this.currentEnvironment = "development";
    }

    public void updateEnvironment(String env) {
        this.currentEnvironment = env; // Allowed because not final
    }
}
```

**Kotlin:**

```kotlin
class ConfigurationService {
    private val apiUrl: String = "https://api.example.com" // Immutable (val)
    private var currentEnvironment: String = "development" // Mutable (var)

    fun updateEnvironment(env: String) {
        currentEnvironment = env // Allowed because it's var
    }
}
```

**Key Differences:**

- `val` in Kotlin = `final` in Java (immutable after assignment)
- `var` in Kotlin = regular mutable variable in Java
- Kotlin makes the distinction clearer and more concise between mutability and immutability

### Static Typing vs Type Inference

**Java:**

```java
public class DataProcessor {
    public List<String> processData() {
        List<String> results = new ArrayList<>(); // Explicit type required
        Map<String, Integer> counts = new HashMap<>(); // Explicit type required

        // Diamond operator since Java 7, but still verbose
        Set<String> uniqueItems = new HashSet<>();

        return results;
    }
}
```

**Kotlin:**

```kotlin
class DataProcessor {
    fun processData(): List<String> {
        val results = mutableListOf<String>() // Type inferred automatically
        val counts = mutableMapOf<String, Int>() // Type inferred

        // Even more concise if context is clear
        val uniqueItems = mutableSetOf<String>()

        return results
    }
}
```

**Key Differences:**

- Kotlin infers types automatically in most cases
- Collection syntax is more concise in Kotlin
- Java requires more explicit type declarations, especially in pre-Java 10 versions

## 🧼 Null Handling

### Null Safety

**Java:**

```java
public class UserProcessor {
    public String processUser(User user) {
        // Defensive programming necessary
        if (user == null) {
            return "No user provided";
        }

        String name = user.getName();
        if (name == null) {
            return "User has no name";
        }

        // Optional for safer APIs (Java 8+)
        Optional<String> email = user.getEmail();
        String emailDisplay = email.map(String::toLowerCase)
            .orElse("no-email@example.com");

        return name.toUpperCase() + " - " + emailDisplay;
    }

    // Using Optional as return type
    public Optional<User> findUserByEmail(String email) {
        if (email == null || email.trim().isEmpty()) {
            return Optional.empty();
        }

        User user = userRepository.findByEmail(email);
        return Optional.ofNullable(user);
    }
}
```

**Kotlin:**

```kotlin
class UserProcessor {
    fun processUser(user: User?): String { // ? indicates it can be null
        // Safe call operator
        val name = user?.name ?: return "No user provided"

        // Multiple safe calls chained
        val emailDisplay = user.email?.lowercase() ?: "no-email@example.com"

        return "${name.uppercase()} - $emailDisplay"
    }

    // Explicit nullable types
    fun findUserByEmail(email: String?): User? {
        if (email.isNullOrBlank()) {
            return null
        }

        return userRepository.findByEmail(email)
    }

    // let to execute only if not null
    fun processUserAdvanced(user: User?) {
        user?.let { safeUser ->
            println("Processing user: ${safeUser.name}")
            safeUser.email?.let { email ->
                sendEmail(email)
            }
        }
    }

    // Not-null assertion operator (use with caution)
    fun processUserUnsafe(user: User?) {
        val name = user!!.name // Throws exception if user is null
        println("User name: $name")
    }
}
```

**Key Differences:**

- Kotlin distinguishes nullable (`String?`) from non-nullable (`String`) types in the type system
- Elvis operator (`?:`) is more concise than Java's if-null checks
- Safe call operator (`?.`) automatically prevents NPE
- `let` allows safe operations on nullable values

### Elvis Operator and Optional Comparison

**Java:**

```java
public class ConfigService {
    public String getConfigValue(String key, String defaultValue) {
        String value = configMap.get(key);
        return Optional.ofNullable(value)
            .filter(v -> !v.trim().isEmpty())
            .orElse(defaultValue);
    }

    public int getUserAge(User user) {
        return Optional.ofNullable(user)
            .map(User::getProfile)
            .map(Profile::getAge)
            .orElse(0);
    }
}
```

**Kotlin:**

```kotlin
class ConfigService {
    fun getConfigValue(key: String, defaultValue: String): String {
        return configMap[key]?.takeIf { it.isNotBlank() } ?: defaultValue
    }

    fun getUserAge(user: User?): Int {
        return user?.profile?.age ?: 0
    }

    // Multiple fallback values
    fun getDisplayName(user: User?): String {
        return user?.displayName
            ?: user?.fullName
            ?: user?.email
            ?: "Anonymous"
    }
}
```

**Key Differences:**

- Kotlin's Elvis operator (`?:`) is more concise than `Optional.orElse()`
- Safe call chains (`?.`) are more natural than `Optional.map()`
- Kotlin allows multiple fallbacks more readably

## 🏎️ Advanced Concepts

- [`🧵 Control Flow`](java_vs_kotlin/ControlFlow.md)
- [`🧱 POJOs vs Data Classes`](java_vs_kotlin/POJO.md)
- [`🧪 Extension Functions`](java_vs_kotlin/ExtensionFunctions.md)
- [`🧰 Collections and Streams`](java_vs_kotlin/Collections.md)
- [`🧵 Threads and Concurrency`](java_vs_kotlin/Concurrency.md)
- [`🧩 Annotations and Frameworks`](java_vs_kotlin/Annotations.md)
- [`🧾 Exception Handling`](java_vs_kotlin/Exceptions.md)
- [`🧠 Functional Programming`](java_vs_kotlin/FunctionalProgramming.md)
- [`🧮 Generic Types`](java_vs_kotlin/Generics.md)
- [`🧭 Reflection`](java_vs_kotlin/Reflection.md)
- [`🧱 Builders and DSLs`](java_vs_kotlin/Builders.md)
- [`🔄 Interoperability`](java_vs_kotlin/Interoperability.md)
