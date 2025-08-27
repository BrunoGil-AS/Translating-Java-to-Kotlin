# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧾 Exception Handling

### try-catch-finally

**Java:**

```java
// Traditional Java try-catch with resource management
public void readFile() {
    FileReader reader = null;
    try {
        reader = new FileReader("file.txt");
        // Process file content
    } catch (FileNotFoundException e) {
        // Handle missing file case
        System.err.println("File not found: " + e.getMessage());
    } finally {
        // Ensure resource cleanup
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
// Kotlin try as an expression with automatic resource management
fun readFile() {
    val content: String? = try {
        // Returns file content or throws IOException
        File("file.txt").readText()
    } catch (e: IOException) {
        // Handle error and return null as fallback
        println("Error reading file: ${e.message}")
        null
    } finally {
        // Cleanup code (if needed)
        println("Finished reading file.")
    }
}
```

**Key Differences:**

- Kotlin's try blocks can be expressions that return values
- No need for explicit resource management (use-statement available)
- More concise error handling with string templates
- Automatic resource cleanup

### Checked vs unchecked exceptions

- **Java** has both checked (must be declared with `throws` or caught) and unchecked (runtime) exceptions.
- **Kotlin does not have checked exceptions**. All exceptions are unchecked. This design decision reduces boilerplate, as you are not forced to catch exceptions you cannot handle.

**Java `throws`:**

```java
// Java requires explicit throws declaration for checked exceptions
public void processFile() throws IOException {
    // Method that must declare checked exceptions
    throw new IOException("Could not process file");
}
```

**Kotlin:**

```kotlin
// Kotlin doesn't require exception declarations
fun processFile() {
    // Can throw any exception without declaration
    throw IOException("Could not process file")
}
```

**Key Differences:**

- Java requires checked exceptions to be declared or caught
- Kotlin treats all exceptions as unchecked
- No need for throws clause in Kotlin
- Simplified exception handling reduces boilerplate
