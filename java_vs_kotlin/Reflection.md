# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧭 Reflection

### Class.forName, Method.invoke

**Java:**

```java
public class ReflectionJava {
    public void inspect() throws Exception {
        Class<?> clazz = Class.forName("java.lang.String");
        Method method = clazz.getMethod("substring", int.class);
        Object result = method.invoke("hello", 2);
        System.out.println(result); // "llo"
    }
}
```

**Kotlin:**

- Kotlin has its own reflection API (`kotlin-reflect`) that provides more idiomatic access to Kotlin-specific concepts like properties and `KClass`.

```kotlin
import kotlin.reflect.full.memberProperties

data class Person(val name: String, val age: Int)

fun inspect() {
    val person = Person("Alice", 30)
    val kClass = person::class
    println(kClass.simpleName) // Person

    kClass.memberProperties.forEach {
        println("${it.name}: ${it.call(person)}")
    }
    // name: Alice
    // age: 30
}
```
