# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧵 Threads and Concurrency

### Thread, Runnable, ExecutorService

**Java:**

- The traditional way of handling concurrency in Java using raw threads or executor services for better management.

```java
public class ConcurrencyJava {
    public void executeTasks() {
        ExecutorService executor = Executors.newFixedThreadPool(5);

        Runnable task = () -> {
            System.out.println("Executing task in thread: " + Thread.currentThread().getName());
        };

        Future<Integer> result = executor.submit(() -> {
            System.out.println("Executing callable task...");
            Thread.sleep(1000);
            return 42;
        });

        executor.submit(task);

        try {
            System.out.println("Callable result: " + result.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }

        executor.shutdown();
    }
}
```

### Coroutines (launch, async, runBlocking)

**Kotlin:**

- Coroutines are lightweight threads that simplify asynchronous programming. They avoid blocking threads and use suspension to allow other tasks to run.

```kotlin
import kotlinx.coroutines.*

fun executeTasks() {
    // GlobalScope is used for simplicity, but structured concurrency is preferred
    GlobalScope.launch { // launch a new coroutine in the background and continue
        delay(1000L)
        println("World!")
    }
    println("Hello,")

    // async starts a coroutine and returns a Deferred value
    val deferred: Deferred<Int> = GlobalScope.async {
        delay(2000L)
        return@async 42
    }

    // runBlocking blocks the current thread until the coroutine completes
    runBlocking {
        println("The answer is ${deferred.await()}")
    }
}
```

### Performance and Readability Comparison

- **Readability**: Coroutines often lead to more readable and sequential-looking code compared to Java's callback-based or `Future`-based asynchronous code.
- **Performance**: Coroutines are more lightweight than threads. Thousands of coroutines can run on a single thread, making them highly efficient for I/O-bound operations where threads would be sitting idle. For CPU-bound tasks, traditional threads might still be necessary to leverage multiple cores fully.
