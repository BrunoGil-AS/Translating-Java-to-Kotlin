# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🔌 Extension Functions en Java

### Comparación de Enfoques

#### Extensiones en Kotlin

```kotlin
// Extensión simple
fun String.addPrefix(prefix: String): String = "$prefix$this"

// Extensión con receptor nulable
fun String?.orEmpty(): String = this ?: ""

// Extensión con generics
fun <T> List<T>.secondOrNull(): T? = getOrNull(1)

// Property extension
val String.firstChar: Char?
    get() = firstOrNull()

// Suspend extension function
suspend fun <T> Flow<T>.collectWithTimeout(timeout: Long) =
    withTimeout(timeout) { collect() }
```

#### Simulando Extensiones en Java

```java
public class StringExtensions {
    public static String addPrefix(String str, String prefix) {
        return prefix + str;
    }

    public static String orEmpty(String str) {
        return str != null ? str : "";
    }
}

public class CollectionExtensions {
    public static <T> T secondOrNull(List<T> list) {
        return list.size() > 1 ? list.get(1) : null;
    }
}
```

### Casos de Uso y Limitaciones

#### Extensiones de Kotlin en Java

```java
// Usando extensiones de Kotlin desde Java
public class JavaUsage {
    public void example() {
        // La extensión se convierte en método estático
        String result = StringExtensionsKt.addPrefix("World", "Hello ");

        // Las property extensions se convierten en getters/setters
        char firstChar = StringExtensionsKt.getFirstChar("Test");
    }
}
```

#### Alternativas en Java

```java
// Usando interfaces default methods (Java 8+)
public interface StringOperations {
    default String addPrefix(String prefix) {
        return prefix + this;
    }
}

public class EnhancedString implements StringOperations {
    private final String value;

    public EnhancedString(String value) {
        this.value = value;
    }
}

// Usando el patrón Decorator
public class StringDecorator {
    private final String value;

    public StringDecorator(String value) {
        this.value = value;
    }

    public String addPrefix(String prefix) {
        return prefix + value;
    }
}
```

### Características Avanzadas

#### Extension Receivers en Kotlin

```kotlin
class HTML {
    fun body() { /*...*/ }
}

// Extension function con receiver
fun HTML.table(init: Table.() -> Unit) {
    val table = Table()
    table.init()
}

// Usage
html {
    table {
        row { cell("Hello") }
    }
}
```

#### Simulación en Java (Verbosa)

```java
public class HTML {
    public void body() { /*...*/ }

    public static Table table(HTML html, Consumer<Table> init) {
        Table table = new Table();
        init.accept(table);
        return table;
    }
}

// Usage
HTML html = new HTML();
HTML.table(html, table -> {
    Table.row(table, row -> {
        row.cell("Hello");
    });
});
```

### Extension Properties

#### Kotlin

```kotlin
// Extension property
val String.vowels: Int
    get() = count { it in "aeiou" }

// Mutable extension property
var StringBuilder.lastChar: Char
    get() = get(length - 1)
    set(value) {
        setCharAt(length - 1, value)
    }
```

#### Java Equivalente

```java
public class StringUtils {
    public static int getVowels(String str) {
        return (int) str.chars()
                       .mapToObj(ch -> (char) ch)
                       .filter(ch -> "aeiou".indexOf(ch) >= 0)
                       .count();
    }
}

public class StringBuilderUtils {
    public static char getLastChar(StringBuilder sb) {
        return sb.charAt(sb.length() - 1);
    }

    public static void setLastChar(StringBuilder sb, char value) {
        sb.setCharAt(sb.length() - 1, value);
    }
}
```

**Key Differences:**

1. **Sintaxis y Usabilidad**

   - Kotlin: Sintaxis natural, como si fuera parte de la clase
   - Java: Requiere clases utilitarias y llamadas estáticas

2. **Capacidades**

   - Kotlin:
     - Extension properties
     - Nullable receiver types
     - Extension functions en scope local
     - DSL building
   - Java:
     - Solo métodos estáticos
     - Default methods en interfaces
     - Más verboso y limitado

3. **Interoperabilidad**

   - Kotlin extensions son accesibles desde Java como métodos estáticos
   - Property extensions se convierten en getters/setters
   - Nombres de archivo especiales para evitar colisiones

4. **Casos de Uso**

   - Kotlin: Ideal para DSLs, mejoras de API, código más limpio
   - Java: Limitado a utilidades y helpers

5. **Rendimiento**

   - Kotlin: Sin overhead, se compila a métodos estáticos
   - Java: Sin overhead con métodos estáticos

6. **Flexibilidad**

   - Kotlin: Puede extender clases sealed y final
   - Java: No puede extender clases finales

7. **Scope y Visibilidad**
   - Kotlin: Control preciso sobre visibilidad y scope
   - Java: Limitado a modificadores de acceso estándar
