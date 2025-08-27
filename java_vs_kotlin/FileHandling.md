# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 📂 File Handling

### Basic File Operations

**Java:**

```java
public class FileHandler {
    public String readFile(String path) throws IOException {
        try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
            StringBuilder content = new StringBuilder();
            String line;
            while ((line = reader.readLine()) != null) {
                content.append(line).append("\n");
            }
            return content.toString();
        }
    }

    public void writeFile(String path, String content) throws IOException {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(path))) {
            writer.write(content);
        }
    }
}
```

**Kotlin:**

```kotlin
class FileHandler {
    fun readFile(path: String): String =
        File(path).readText()

    fun writeFile(path: String, content: String) {
        File(path).writeText(content)
    }

    // Reading lines as sequence for memory efficiency
    fun processLargeFile(path: String) {
        File(path).useLines { lines ->
            lines.forEach { line ->
                // Process each line
            }
        }
    }
}
```

### Resource Management

**Java:**

```java
public class ResourceManager {
    public List<String> readLines(String path) throws IOException {
        try (BufferedReader reader = Files.newBufferedReader(Paths.get(path))) {
            return reader.lines().collect(Collectors.toList());
        }
    }

    public void copyFile(String source, String target) throws IOException {
        try (InputStream in = new FileInputStream(source);
             OutputStream out = new FileOutputStream(target)) {
            byte[] buffer = new byte[1024];
            int length;
            while ((length = in.read(buffer)) > 0) {
                out.write(buffer, 0, length);
            }
        }
    }
}
```

**Kotlin:**

```kotlin
class ResourceManager {
    fun readLines(path: String): List<String> =
        File(path).useLines { it.toList() }

    fun copyFile(source: String, target: String) {
        File(source).inputStream().use { input ->
            File(target).outputStream().use { output ->
                input.copyTo(output)
            }
        }
    }
}
```

### Advanced File Operations

**Java:**

```java
public class AdvancedFileOps {
    public void walkDirectory(Path dir) throws IOException {
        Files.walk(dir)
             .filter(Files::isRegularFile)
             .forEach(System.out::println);
    }

    public void watchDirectory(Path dir) throws IOException {
        WatchService watchService = FileSystems.getDefault().newWatchService();
        dir.register(watchService, StandardWatchEventKinds.ENTRY_CREATE);

        while (true) {
            WatchKey key = watchService.take();
            key.pollEvents().forEach(event -> {
                // Handle event
            });
            key.reset();
        }
    }
}
```

**Kotlin:**

```kotlin
class AdvancedFileOps {
    fun walkDirectory(dir: File) {
        dir.walk()
           .filter { it.isFile }
           .forEach { println(it) }
    }

    fun watchDirectory(dir: File) {
        val watchService = FileSystems.getDefault().newWatchService()
        val path = dir.toPath()
        path.register(watchService, StandardWatchEventKinds.ENTRY_CREATE)

        while (true) {
            val key = watchService.take()
            key.pollEvents().forEach { event ->
                // Handle event
            }
            key.reset()
        }
    }

    // Extension function for temporary file handling
    fun File.createTempCopy(): File =
        createTempFile(nameWithoutExtension, ".$extension").also {
            this.copyTo(it, overwrite = true)
        }
}
```

**Key Differences:**

- Kotlin provides simple extension functions for common operations
- Built-in resource management with `use` function
- More functional approach with sequences (`useLines`)
- Extension functions make operations more intuitive
- No checked exceptions in Kotlin
- Built-in memory efficient operations
- File walking and traversal is simpler
- Better support for coroutines in file operations
