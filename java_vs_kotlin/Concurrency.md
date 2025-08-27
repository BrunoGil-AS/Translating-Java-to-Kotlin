# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🧵 Threads and Concurrency

### Thread States and Lifecycle

**Java:**

A thread in Java can be in one of six states during its lifecycle:

1. **NEW**: Thread is created but not yet started
2. **RUNNABLE**: Thread is executing or ready to execute
3. **BLOCKED**: Thread is waiting for a monitor lock
4. **WAITING**: Thread is waiting indefinitely for another thread
5. **TIMED_WAITING**: Thread is waiting for a specified time
6. **TERMINATED**: Thread has completed its execution

```java
public class ThreadStatesDemo {
    public void demonstrateStates() {
        Thread thread = new Thread(() -> {
            try {
                Thread.sleep(1000); // TIMED_WAITING state
                synchronized(this) {
                    wait(); // WAITING state
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        System.out.println(thread.getState()); // NEW
        thread.start(); // RUNNABLE
    }
}
```

**Kotlin:**

Kotlin coroutines have similar states but are managed differently:

1. **Created**: Coroutine is created but not started
2. **Active**: Coroutine is running
3. **Suspended**: Coroutine is temporarily paused
4. **Completed**: Coroutine has finished execution
5. **Cancelled**: Coroutine was cancelled before completion

```kotlin
fun demonstrateCoroutineStates() = runBlocking {
    val job = launch {
        try {
            delay(1000) // Suspended state
            yield() // Cooperative cancellation check
        } catch (e: CancellationException) {
            println("Coroutine was cancelled!")
        }
    }

    println(job.isActive) // true - Active state
    delay(500)
    job.cancel() // Cancelled state
    job.join() // Wait for completion
}
```

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
