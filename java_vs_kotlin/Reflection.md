# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧭 Reflection

Reflection allows a program to **inspect and manipulate classes, methods, and properties at runtime**, which is useful for frameworks, serialization, and dynamic behavior.

### Class.forName & Method.invoke

**Java:**

- Java reflection uses the `Class` and `Method` APIs to load classes dynamically and invoke methods.
- This approach works for Java classes and methods but is less idiomatic for Kotlin-specific features.

```java
public class ReflectionJava {
    public void inspect() throws Exception {
        // Load class dynamically
        Class<?> clazz = Class.forName("java.lang.String");

        // Retrieve method by name and parameter type
        Method method = clazz.getMethod("substring", int.class);

        // Invoke method on an instance
        Object result = method.invoke("hello", 2);
        System.out.println(result); // Outputs: "llo"
    }
}
```

**Kotlin:**

- Kotlin provides the **`kotlin-reflect`** library for idiomatic reflection.
- Uses `KClass` to represent Kotlin classes and `memberProperties` to access properties dynamically.
- More suitable for Kotlin-specific constructs like `data class` properties and delegated properties.

```kotlin
import kotlin.reflect.full.memberProperties

data class Person(val name: String, val age: Int)

fun inspect() {
    val person = Person("Alice", 30)
    val kClass = person::class

    // Class name
    println(kClass.simpleName) // Outputs: Person

    // Iterate over properties and get values dynamically
    kClass.memberProperties.forEach {
        println("${it.name}: ${it.call(person)}")
    }
    // Outputs:
    // name: Alice
    // age: 30
}
```

**Key Differences:**

- Java reflection uses **`Class` and `Method`**, Kotlin uses **`KClass` and property references**.
- Kotlin reflection is more **type-safe and idiomatic**, especially for data classes and Kotlin-specific features.
- Kotlin requires the `kotlin-reflect` library for full reflection capabilities.
- Reflection in Kotlin allows direct access to properties, whereas Java reflection often requires method invocation.

---

### Summary

- **Java**: Reflection works via `Class.forName`, `getMethod`, and `invoke`. Suitable for dynamic Java code.
- **Kotlin**: Reflection via `kotlin-reflect` provides a more **expressive, type-safe, and Kotlin-friendly API** to inspect classes and properties at runtime.
- Kotlin reflection integrates seamlessly with **data classes, delegated properties, and functions**, making it more versatile for modern backend development.
