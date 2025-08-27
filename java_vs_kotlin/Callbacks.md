# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 📞 Callbacks

### Callbacks Básicos

#### Java

```java
// Interfaz de callback
public interface DataCallback<T> {
    void onSuccess(T data);
    void onError(Exception error);
}

// Clase que usa el callback
public class AsyncDataLoader {
    public void loadData(DataCallback<String> callback) {
        try {
            // Simular operación asíncrona
            String data = fetchData();
            callback.onSuccess(data);
        } catch (Exception e) {
            callback.onError(e);
        }
    }
}

// Uso del callback
public class Example {
    public void example() {
        AsyncDataLoader loader = new AsyncDataLoader();

        loader.loadData(new DataCallback<String>() {
            @Override
            public void onSuccess(String data) {
                System.out.println("Data: " + data);
            }

            @Override
            public void onError(Exception error) {
                error.printStackTrace();
            }
        });

        // Java 8+ con lambdas
        loader.loadData(new DataCallback<String>() {
            @Override
            public void onSuccess(String data) {
                System.out.println("Data: " + data);
            }

            @Override
            public void onError(Exception error) {
                error.printStackTrace();
            }
        });
    }
}
```

#### Kotlin

```kotlin
// Tipo función para callbacks
typealias DataCallback<T> = (Result<T>) -> Unit

// Clase que usa el callback
class AsyncDataLoader {
    fun loadData(callback: DataCallback<String>) {
        try {
            // Simular operación asíncrona
            val data = fetchData()
            callback(Result.success(data))
        } catch (e: Exception) {
            callback(Result.failure(e))
        }
    }

    // Versión con suspend function
    suspend fun loadDataCoroutine(): String =
        withContext(Dispatchers.IO) {
            fetchData()
        }
}

// Uso del callback
class Example {
    fun example() {
        val loader = AsyncDataLoader()

        // Callback simple
        loader.loadData { result ->
            result.fold(
                onSuccess = { data -> println("Data: $data") },
                onFailure = { error -> error.printStackTrace() }
            )
        }

        // Usando runCatching
        loader.loadData { result ->
            result
                .onSuccess { data -> println("Data: $data") }
                .onFailure { error -> error.printStackTrace() }
        }

        // Con coroutines
        GlobalScope.launch {
            try {
                val data = loader.loadDataCoroutine()
                println("Data: $data")
            } catch (e: Exception) {
                e.printStackTrace()
            }
        }
    }
}
```

### Callbacks Encadenados

#### Java

```java
public class ChainedCallbacks {
    public interface Step1Callback {
        void onComplete(String result);
        void onError(Exception error);
    }

    public interface Step2Callback {
        void onComplete(Integer result);
        void onError(Exception error);
    }

    public void processSteps() {
        step1(new Step1Callback() {
            @Override
            public void onComplete(String result) {
                step2(result, new Step2Callback() {
                    @Override
                    public void onComplete(Integer result) {
                        System.out.println("Final result: " + result);
                    }

                    @Override
                    public void onError(Exception error) {
                        error.printStackTrace();
                    }
                });
            }

            @Override
            public void onError(Exception error) {
                error.printStackTrace();
            }
        });
    }
}
```

#### Kotlin

```kotlin
class ChainedCallbacks {
    // Usando tipos función
    typealias Step1Callback = (Result<String>) -> Unit
    typealias Step2Callback = (Result<Int>) -> Unit

    // Versión con callbacks
    fun processSteps() {
        step1 { result1 ->
            result1.fold(
                onSuccess = { str ->
                    step2(str) { result2 ->
                        result2.fold(
                            onSuccess = { num ->
                                println("Final result: $num")
                            },
                            onFailure = { it.printStackTrace() }
                        )
                    }
                },
                onFailure = { it.printStackTrace() }
            )
        }
    }

    // Versión con coroutines
    suspend fun processStepsCoroutine() {
        try {
            val step1Result = step1Coroutine()
            val step2Result = step2Coroutine(step1Result)
            println("Final result: $step2Result")
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
}
```

### Event Listeners

#### Java

```java
public class EventEmitter {
    private List<EventListener> listeners = new ArrayList<>();

    public interface EventListener {
        void onEvent(String event);
    }

    public void addEventListener(EventListener listener) {
        listeners.add(listener);
    }

    public void removeEventListener(EventListener listener) {
        listeners.remove(listener);
    }

    public void emit(String event) {
        for (EventListener listener : listeners) {
            listener.onEvent(event);
        }
    }
}

// Uso
public class EventExample {
    public void example() {
        EventEmitter emitter = new EventEmitter();

        emitter.addEventListener(event ->
            System.out.println("Event received: " + event)
        );
    }
}
```

#### Kotlin

```kotlin
class EventEmitter {
    private val listeners = mutableListOf<(String) -> Unit>()

    fun addEventListener(listener: (String) -> Unit) {
        listeners.add(listener)
    }

    fun removeEventListener(listener: (String) -> Unit) {
        listeners.remove(listener)
    }

    fun emit(event: String) {
        listeners.forEach { it(event) }
    }

    // Versión con Flow
    fun events(): Flow<String> = callbackFlow {
        val listener: (String) -> Unit = { event ->
            trySend(event)
        }

        addEventListener(listener)
        awaitClose {
            removeEventListener(listener)
        }
    }
}

// Uso
class EventExample {
    fun example() {
        val emitter = EventEmitter()

        // Callback style
        emitter.addEventListener { event ->
            println("Event received: $event")
        }

        // Flow style
        GlobalScope.launch {
            emitter.events()
                .collect { event ->
                    println("Event received: $event")
                }
        }
    }
}
```

**Key Differences:**

1. **Sintaxis y Concisión**

   - Kotlin usa tipos función en lugar de interfaces
   - Lambdas más concisos
   - Type aliases para callbacks complejos

2. **Manejo de Errores**

   - Kotlin usa Result type
   - Better exception handling with coroutines
   - Más opciones para manejo de errores

3. **Asincronía**

   - Java usa callbacks anidados
   - Kotlin ofrece coroutines y Flow
   - Menos callback hell en Kotlin

4. **Tipo Sistema**

   - Kotlin tiene mejor inferencia de tipos
   - Null safety incorporado
   - Type aliases para simplificar signatures

5. **Patrones de Diseño**

   - Java usa interfaces funcionales
   - Kotlin prefiere high-order functions
   - Kotlin soporta DSLs

6. **Reactive Programming**

   - Java necesita RxJava o similar
   - Kotlin tiene Flow nativo
   - Mejor integración con coroutines

7. **Testing**
   - Kotlin facilitates coroutine testing
   - Less mocking required
   - TestCoroutineDispatcher for control

### Android Lifecycle Callbacks

#### Java

```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    protected void onResume() {
        super.onResume();
        registerSensorCallback(new SensorEventListener() {
            @Override
            public void onSensorChanged(SensorEvent event) {
                // Handle sensor data
            }

            @Override
            public void onAccuracyChanged(Sensor sensor, int accuracy) {
                // Handle accuracy changes
            }
        });
    }

    @Override
    protected void onPause() {
        super.onPause();
        unregisterSensorCallback();
    }
}
```

#### Kotlin

```kotlin
class MainActivity : AppCompatActivity() {
    private val lifecycleScope = lifecycleScope

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Lifecycle-aware coroutine
        lifecycleScope.launch {
            // Will automatically cancel when activity is destroyed
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                // Collect Flow or perform other async operations
            }
        }
    }

    override fun onResume() {
        super.onResume()
        // Using SAM conversion for listeners
        registerSensorCallback {
            onSensorChanged { event ->
                // Handle sensor data
            }
            onAccuracyChanged { sensor, accuracy ->
                // Handle accuracy changes
            }
        }
    }

    // LifecycleOwner integration
    private val locationCallback = object : LocationCallback() {
        override fun onLocationResult(result: LocationResult) {
            // Process location updates
        }
    }.apply {
        // Automatically manage callback lifecycle
        lifecycle.addObserver(LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_DESTROY) {
                removeLocationUpdates(this)
            }
        })
    }
}
```

**Key Differences for Android:**

1. **Lifecycle Integration**

   - Java requires manual lifecycle management
   - Kotlin uses LifecycleScope and coroutines
   - Automatic cleanup with structured concurrency

2. **Callback Syntax**

   - Kotlin's SAM conversion for listeners
   - DSL-style callback registration
   - Better type inference

3. **Scope Management**

   - Kotlin coroutines respect lifecycle
   - Automatic cancellation on destroy
   - ViewModelScope and lifecycleScope

4. **Event Handling**

   - Flow for reactive streams
   - StateFlow and SharedFlow
   - Channel for hot streams

5. **Error Handling**
   - CoroutineExceptionHandler
   - Structured error handling
   - Supervisor scope for isolation
