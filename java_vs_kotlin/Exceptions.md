# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧾 Exception Handling

### try-catch-finally

**Java:**

```java
public void readFile() {
    FileReader reader = null;
    try {
        reader = new FileReader("file.txt");
        // ... read file
    } catch (FileNotFoundException e) {
        System.err.println("File not found: " + e.getMessage());
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**Kotlin:**

- `try` is an expression in Kotlin, meaning it can return a value.

```kotlin
fun readFile() {
    val content: String? = try {
        File("file.txt").readText()
    } catch (e: IOException) {
        println("Error reading file: ${e.message}")
        null
    } finally {
        println("Finished reading file.")
    }
}
```

### Checked vs unchecked exceptions

- **Java** has both checked (must be declared with `throws` or caught) and unchecked (runtime) exceptions.
- **Kotlin does not have checked exceptions**. All exceptions are unchecked. This design decision reduces boilerplate, as you are not forced to catch exceptions you cannot handle.

**Java `throws`:**

```java
public void processFile() throws IOException {
    // ... code that might throw IOException
}
```

**Kotlin:**

```kotlin
// No 'throws' declaration needed
fun processFile() {
    throw IOException("Could not process file")
}
```
