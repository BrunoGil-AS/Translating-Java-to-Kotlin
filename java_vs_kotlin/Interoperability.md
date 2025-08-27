# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🔄 Interoperability

### Calling Java code from Kotlin and vice versa

- **Kotlin to Java**: Seamless. Kotlin code compiles to JVM bytecode and can be called from Java just like any other Java code.
- **Java to Kotlin**: Mostly seamless. Kotlin properties are exposed to Java as getter/setter methods.

**Kotlin code:**

```kotlin
class Greeter(val name: String) {
    fun greet() {
        println("Hello, $name")
    }
}
```

**Java calling Kotlin:**

```java
public class JavaMain {
    public static void main(String[] args) {
        Greeter greeter = new Greeter("World"); // Create Kotlin object
        System.out.println(greeter.getName()); // Use getter
        greeter.greet(); // Call method
    }
}
```

### Limitations with Lombok

- Since both Lombok (Java) and Kotlin generate bytecode during compilation, there can be conflicts in the compilation order. It is generally recommended to avoid using Lombok in Kotlin projects or in Java modules that are dependencies of Kotlin modules.

### Compilation order and common problems

- In mixed-language projects, the Java compiler and Kotlin compiler must run in the correct order. Build tools like Maven and Gradle handle this automatically, but misconfiguration can lead to "unresolved reference" errors.
- **Nullability**: When calling Java code from Kotlin, all types are "platform types" (e.g., `String!`), meaning Kotlin doesn't know if they are nullable or not. It is the developer's responsibility to handle potential nulls.
