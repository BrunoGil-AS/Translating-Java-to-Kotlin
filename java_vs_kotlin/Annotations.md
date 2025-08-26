[`<--`](../JavaVsKotlin.md)

## 🧩 Annotations and Frameworks

### Using annotations in Spring Boot

**Java:**

```java
@Service
public class MyService {

    private final MyRepository myRepository;

    @Autowired
    public MyService(MyRepository myRepository) {
        this.myRepository = myRepository;
    }

    public String getMessage() {
        return myRepository.getData();
    }
}

@Repository
public class MyRepository {
    public String getData() {
        return "Data from repository";
    }
}
```

**Kotlin:**

- Kotlin works seamlessly with Spring Boot. The syntax is more concise due to primary constructors.

```kotlin
@Service
class MyService(private val myRepository: MyRepository) { // @Autowired is optional on constructors
    fun getMessage(): String {
        return myRepository.getData()
    }
}

@Repository
class MyRepository {
    fun getData(): String {
        return "Data from repository"
    }
}
```

### Bean and dependency configuration

**Java:**

```java
@Configuration
public class AppConfig {
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

**Kotlin:**

```kotlin
@Configuration
class AppConfig {
    @Bean
    fun restTemplate(): RestTemplate {
        return RestTemplate()
    }
}
```
