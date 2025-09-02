# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

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

---

### Creating Threads

#### 1. Extending the **Thread** Class

**Java:**

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running in Java");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

**Kotlin:**

```kotlin
class MyThread : Thread() {
    override fun run() {
        println("Thread running in Kotlin")
    }
}

fun main() {
    val t = MyThread()
    t.start()
}
```

---

#### 2. Implementing the **Runnable** Interface

**Java:**

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable task running in Java");
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(new MyRunnable());
        t.start();
    }
}
```

**Kotlin:**

```kotlin
class MyRunnable : Runnable {
    override fun run() {
        println("Runnable task running in Kotlin")
    }
}

fun main() {
    val t = Thread(MyRunnable())
    t.start()
}
```

---

#### 3. Using Lambdas (Runnable Simplified)

**Java:**

```java
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("Lambda Runnable in Java");
        });
        t.start();
    }
}
```

**Kotlin:**

```kotlin
fun main() {
    val t = Thread {
        println("Lambda Runnable in Kotlin")
    }
    t.start()
}
```

👉 Kotlin uses **SAM (Single Abstract Method) conversions**, making thread creation shorter and cleaner.

---

### Synchronization: **wait & notify**

If multiple threads share an object, one can `wait()` while another `notify()` it.

**Java:**

```java
class Shared {
    synchronized void waitForSignal() throws InterruptedException {
        wait(); // current thread waits
    }

    synchronized void sendSignal() {
        notify(); // wakes one waiting thread
    }
}
```

**Kotlin:**

```kotlin
class Shared {
    @Synchronized fun waitForSignal() {
        try {
            (this as java.lang.Object).wait()
        } catch (e: InterruptedException) {
            e.printStackTrace()
        }
    }

    @Synchronized fun sendSignal() {
        (this as java.lang.Object).notify()
    }
}
```

👉 Notes:

- Kotlin compiles to JVM bytecode, so `wait()` / `notify()` still work (they belong to `java.lang.Object`).
- Use `@Synchronized` to make functions thread-safe.

---

### Example: Two Threads Communicating

```kotlin
class Shared {
    @Synchronized fun waitForSignal() {
        println("Waiting...")
        (this as java.lang.Object).wait()
        println("Resumed!")
    }

    @Synchronized fun sendSignal() {
        println("Sending signal...")
        (this as java.lang.Object).notify()
    }
}

fun main() {
    val shared = Shared()

    val t1 = Thread { shared.waitForSignal() }
    val t2 = Thread {
        Thread.sleep(1000) // simulate work
        shared.sendSignal()
    }

    t1.start()
    t2.start()
}
```

**Output:**

```shell
Waiting...
Sending signal...
Resumed!
```

---

### Coroutines (launch, async, runBlocking)

**Kotlin:**

Coroutines are lightweight threads that simplify asynchronous programming. They avoid blocking threads and use suspension to allow other tasks to run.

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

**Another Example with Structured Concurrency:**

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch {
        delay(1000)
        println("Coroutine 1 finished")
    }
    launch {
        println("Coroutine 2 finished immediately")
    }
}
```

---

### Performance and Readability Comparison

- **Readability**: Coroutines lead to more sequential-looking and cleaner code compared to Java's callback-based or `Future`-based async code.
- **Performance**: Coroutines are more lightweight than threads. Thousands of coroutines can run on a few threads, making them highly efficient for I/O-bound operations.

---

### 📊 Summary

| **Approach**         | **Java**                               | **Kotlin**                                     |
| -------------------- | -------------------------------------- | ---------------------------------------------- |
| Extend `Thread`      | `class MyThread extends Thread {}`     | `class MyThread : Thread() {}`                 |
| Implement `Runnable` | `class MyRunnable implements Runnable` | `class MyRunnable : Runnable`                  |
| Lambda Runnable      | `Thread(() -> { ... })`                | `Thread { ... }`                               |
| Classic Sync         | `wait()` / `notify()`                  | `wait()` / `notify()` inside `@Synchronized`   |
| Modern Concurrency   | Executors, Futures, Streams            | ✅ **Coroutines** (`launch`, `async`, `delay`) |
