# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧱 Builders and DSLs

### Builder pattern in Java

**Java:**

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

### Kotlin DSLs (apply, with, run, also, let)

- Kotlin's scope functions and lambda syntax make it easy to create type-safe, structured builders (DSLs).

```kotlin
data class User(var name: String = "", var age: Int = 0)

// Usage with 'apply'
val user = User().apply {
    name = "John"
    age = 30
}
```
