# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧠 Functional Programming

Functional programming emphasizes **treating functions as first-class citizens**, allowing them to be passed around, returned, and composed.

### Lambdas & Higher-Order Functions

**Java:**

- Java functions are **not first-class citizens**, but you can represent them using **functional interfaces** like `Function`, `Predicate`, or `Consumer`.
- **Higher-order functions** are simulated by passing functional interface instances as parameters.

```java
// Higher-order function example in Java using functional interfaces
public class HigherOrderFunctionExample {
    // Method that takes a function as parameter
    public void process(String value, Function<String, Integer> processor) {
        // Apply the function and print result
        System.out.println(processor.apply(value));
    }

    public void usage() {
        // Pass lambda as function parameter
        process("hello", s -> s.length()); // Outputs: 5

        // Using method reference for cleaner syntax
        process("world", String::length); // Outputs: 5
    }
}
```

**Kotlin:**

- Functions are **first-class citizens**: they can be stored in variables, passed as arguments, and returned from other functions.
- Lambda expressions are concise, and the **`it`** keyword can be used for single-parameter lambdas.

```kotlin
// Higher-order functions in Kotlin with function types
class HigherOrderFunctionExample {
    // Function type (String) -> Int as parameter
    fun process(value: String, processor: (String) -> Int) {
        println(processor(value))
    }

    fun usage() {
        // Using lambda with implicit parameter 'it'
        process("hello") { it.length } // Outputs: 5

        // Using function reference
        process("world", String::length) // Outputs: 5

        // Storing function in variable for reuse
        val lengthCalculator: (String) -> Int = { it.length }
        process("kotlin", lengthCalculator) // Outputs: 6
    }
}
```

**Key Differences:**

- Kotlin has **native function types**, eliminating the need for functional interfaces.
- Lambdas are **more concise**, especially with the `it` keyword.
- Functions can be **stored in variables** and passed freely.
- Higher-order functions are **naturally supported**, making code more expressive.

---

### Immutability

Immutability is a core concept in functional programming that helps **avoid side-effects** and makes code easier to reason about.

**Java:**

- Java requires explicit effort to create immutable objects.
- Use `final` fields and defensive copying for collections.

```java
public final class ImmutablePerson {
    private final String name;
    private final int age;
    private final List<String> hobbies;

    public ImmutablePerson(String name, int age, List<String> hobbies) {
        this.name = name;
        this.age = age;
        this.hobbies = List.copyOf(hobbies); // Immutable copy
    }

    // Only getters, no setters
    public String getName() { return name; }
    public int getAge() { return age; }
    public List<String> getHobbies() { return hobbies; }
}
```

**Kotlin:**

- Kotlin makes immutability **more natural** with `val` and immutable collections.
- **Data classes** provide concise syntax with built-in immutability and component functions.

```kotlin
// Immutable data class in Kotlin
data class Person(
    val name: String,
    val age: Int,
    val hobbies: List<String>  // Immutable by default
)

fun demo() {
    val person = Person("Alice", 30, listOf("reading", "hiking"))
    // person.name = "Bob"  // Compilation error: val cannot be reassigned
    // person.hobbies.add("swimming")  // Compilation error: List is immutable
}
```

**Key Differences:**

- Kotlin uses **`val`** for read-only properties.
- Collections are **immutable by default**, reducing the need for defensive copying.
- **Data classes** make creating immutable objects concise and readable.
- Kotlin’s syntax is more **declarative and less boilerplate-heavy** than Java.

---

### Summary

- **Functional Programming in Java**: achieved through functional interfaces and lambdas (since Java 8), but less natural.
- **Functional Programming in Kotlin**: fully supported with first-class functions, concise lambdas, higher-order functions, and immutable data classes.
- Kotlin’s design encourages **pure functions**, immutability, and functional-style programming, making backend code safer and easier to maintain.
