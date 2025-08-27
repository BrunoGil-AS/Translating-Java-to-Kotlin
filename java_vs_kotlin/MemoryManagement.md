# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧮 Memory Management

### Memory Model

**Java:**

```java
public class MemoryExample {
    // Stack: primitives and references
    private int counter = 0;
    private String name = "John";

    // Heap: objects
    private List<String> items = new ArrayList<>();

    public void demonstrateMemory() {
        // Stack: local variables
        int localVar = 42;

        // Heap: new object allocation
        String newString = new String("Hello");

        // Reference to heap object
        Object obj = new Object();
    }
}
```

**Kotlin:**

```kotlin
class MemoryExample {
    // Stack: primitives (internally)
    private var counter = 0

    // All Kotlin properties are objects
    private var name = "John"

    // Immutable list on heap
    private val items = listOf<String>()

    fun demonstrateMemory() {
        // Local variables
        val localVar = 42 // Still primitive internally

        // String on heap
        val newString = String("Hello")

        // Reference type
        val obj: Any = Object()
    }
}
```

### Resource Management

**Java:**

```java
public class ResourceManager implements AutoCloseable {
    private final FileInputStream stream;

    public ResourceManager(String path) throws IOException {
        this.stream = new FileInputStream(path);
    }

    public void process() throws IOException {
        try (ResourceManager manager = new ResourceManager("file.txt")) {
            // Use resource
        }
    }

    @Override
    public void close() throws IOException {
        stream.close();
    }
}
```

**Kotlin:**

```kotlin
class ResourceManager(path: String) : AutoCloseable {
    private val stream = FileInputStream(path)

    fun process() {
        ResourceManager("file.txt").use { manager ->
            // Use resource
        }
    }

    override fun close() {
        stream.close()
    }
}
```

### Garbage Collection

**Java:**

```java
public class GCExample {
    private WeakReference<Resource> weakRef;
    private SoftReference<CacheData> softRef;

    public void manageReferences() {
        // Strong reference - prevents GC
        Resource resource = new Resource();

        // Weak reference - collected when no strong refs exist
        weakRef = new WeakReference<>(resource);

        // Soft reference - collected when memory is tight
        softRef = new SoftReference<>(new CacheData());

        // Phantom reference - for cleanup actions
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
        PhantomReference<Object> phantomRef =
            new PhantomReference<>(new Object(), queue);
    }
}
```

**Kotlin:**

```kotlin
class GCExample {
    private var weakRef: WeakReference<Resource>? = null
    private var softRef: SoftReference<CacheData>? = null

    fun manageReferences() {
        // Strong reference
        val resource = Resource()

        // Weak reference with safe call operator
        weakRef = WeakReference(resource)

        // Soft reference for caching
        softRef = SoftReference(CacheData())

        // Phantom reference with queue
        val queue = ReferenceQueue<Any>()
        val phantomRef = PhantomReference(Any(), queue)
    }
}
```

### Memory Optimization

**Java:**

```java
public class MemoryOptimization {
    // Value class in Java (16+)
    public record Point(int x, int y) {}

    // Object pooling
    private static final Pool<StringBuilder> BUILDER_POOL =
        new Pool<>(() -> new StringBuilder());

    public String process() {
        StringBuilder builder = BUILDER_POOL.acquire();
        try {
            return builder.append("result").toString();
        } finally {
            BUILDER_POOL.release(builder);
        }
    }
}
```

**Kotlin:**

```kotlin
class MemoryOptimization {
    // Value class - no runtime allocation
    @JvmInline
    value class Point(private val packedValue: Long) {
        val x: Int get() = (packedValue shr 32).toInt()
        val y: Int get() = packedValue.toInt()
    }

    // Object pooling with sequence
    private val builderPool = sequence {
        while (true) yield(StringBuilder())
    }.iterator()

    fun process(): String = builderPool.next().apply {
        append("result")
    }.toString()
}
```

**Key Differences:**

- Kotlin simplifies resource management with `use`
- Value classes optimize memory usage
- Both use JVM garbage collection
- Kotlin has better null safety
- Reference types are handled similarly
- Kotlin properties are objects
- Extension functions avoid utility classes
- Smart casts reduce temporary objects
