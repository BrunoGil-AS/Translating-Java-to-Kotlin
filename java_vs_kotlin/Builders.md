# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧱 Builders and DSLs

### Builder Pattern in Java

- The Builder pattern is a **creational design pattern** that allows constructing complex objects step by step.
- It helps **avoid constructors with many parameters** and supports **immutable objects**.

```java
public class User {
    private final String name;
    private final int age;

    private User(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
    }

    public static class Builder {
        private String name;
        private int age;

        public Builder name(String name) {
            this.name = name;
            return this;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}

// Usage:
// User user = new User.Builder().name("John").age(30).build();
```

**Key Points:**

- Provides a **fluent interface** for creating objects.
- Useful for **immutable classes** with multiple optional parameters.
- Requires additional boilerplate code for the builder class.

---

### Kotlin DSLs (apply, with, run, also, let)

- Kotlin uses **scope functions** and **lambda expressions** to simplify object construction.
- This approach leads to **type-safe, readable, and concise DSLs (Domain Specific Languages)**.

```kotlin
data class User(var name: String = "", var age: Int = 0)

// Usage with 'apply' for builder-style DSL
val user = User().apply {
    name = "John"
    age = 30
}
```

**Explanation of Scope Functions:**

- **`apply`**: Executes block with `this` as receiver, returns the object (ideal for builders).
- **`with`**: Calls a block on an object, returns the result of the block.
- **`run`**: Similar to `with`, but invoked as an extension and returns the block result.
- **`also`**: Executes block with `it` as parameter, returns the object (for side-effects).
- **`let`**: Executes block with `it` as parameter, returns the block result (useful for null-safety and transformations).

**Key Differences:**

- Kotlin reduces boilerplate with concise **scope functions** instead of separate builder classes.
- DSLs in Kotlin are **type-safe and readable**, enabling structured configuration and initialization.
- Works naturally with **mutable or immutable data classes** without needing explicit setters or builder classes.
