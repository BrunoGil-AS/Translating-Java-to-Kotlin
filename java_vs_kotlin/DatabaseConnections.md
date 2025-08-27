# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🗄️ Database Connections

### JDBC Connections

**Java:**

```java
public class DatabaseConnection {
    private static final String URL = "jdbc:postgresql://localhost:5432/db";

    public Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, "user", "password");
    }

    public void executeQuery() {
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(
                 "SELECT * FROM users WHERE age > ?")) {

            stmt.setInt(1, 18);
            ResultSet rs = stmt.executeQuery();

            while (rs.next()) {
                String name = rs.getString("name");
                int age = rs.getInt("age");
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

**Kotlin:**

```kotlin
class DatabaseConnection {
    private val url = "jdbc:postgresql://localhost:5432/db"

    fun getConnection(): Connection =
        DriverManager.getConnection(url, "user", "password")

    fun executeQuery() {
        getConnection().use { conn ->
            conn.prepareStatement(
                "SELECT * FROM users WHERE age > ?"
            ).use { stmt ->
                stmt.setInt(1, 18)
                stmt.executeQuery().use { rs ->
                    while (rs.next()) {
                        val name = rs.getString("name")
                        val age = rs.getInt("age")
                    }
                }
            }
        }
    }
}
```

### Spring Data JPA

**Java:**

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private int age;

    // Getters, setters, constructors
}

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByAgeGreaterThan(int age);

    @Query("SELECT u FROM User u WHERE u.name LIKE :pattern")
    List<User> searchByNamePattern(@Param("pattern") String pattern);
}

@Service
public class UserService {
    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public User createUser(User user) {
        return repository.save(user);
    }
}
```

**Kotlin:**

```kotlin
@Entity
data class User(
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long? = null,

    val name: String,
    val age: Int
)

@Repository
interface UserRepository : JpaRepository<User, Long> {
    fun findByAgeGreaterThan(age: Int): List<User>

    @Query("SELECT u FROM User u WHERE u.name LIKE :pattern")
    fun searchByNamePattern(@Param("pattern") pattern: String): List<User>
}

@Service
class UserService(private val repository: UserRepository) {
    @Transactional
    fun createUser(user: User): User = repository.save(user)
}
```

### Exposed (Kotlin SQL Framework)

```kotlin
// Table definition
object Users : Table() {
    val id = long("id").autoIncrement()
    val name = varchar("name", 255)
    val age = integer("age")

    override val primaryKey = PrimaryKey(id)
}

// DAO
class UserDao : BaseDao() {
    fun createUser(name: String, age: Int) = dbQuery {
        Users.insert {
            it[Users.name] = name
            it[Users.age] = age
        }
    }

    fun findUsersOlderThan(age: Int) = dbQuery {
        Users.select { Users.age greater age }
            .map { User(
                id = it[Users.id],
                name = it[Users.name],
                age = it[Users.age]
            )}
    }
}

// Transaction and connection management
object DatabaseFactory {
    fun init() {
        Database.connect(
            url = "jdbc:postgresql://localhost:5432/db",
            driver = "org.postgresql.Driver",
            user = "user",
            password = "password"
        )

        transaction {
            SchemaUtils.create(Users)
        }
    }
}
```

### Coroutine Support

```kotlin
// Repository with suspend functions
@Repository
interface UserRepository : JpaRepository<User, Long> {
    // Coroutine support with Spring
    suspend fun findByEmail(email: String): User?

    // Flow support for reactive streams
    fun findAllByAgeGreaterThan(age: Int): Flow<User>
}

// Service using coroutines
@Service
class UserService(private val repository: UserRepository) {
    suspend fun processUsers(age: Int) = coroutineScope {
        repository.findAllByAgeGreaterThan(age)
            .collect { user ->
                launch {
                    processUserAsync(user)
                }
            }
    }

    // R2DBC support for reactive database access
    suspend fun findUserById(id: Long): User = withContext(Dispatchers.IO) {
        repository.findById(id).awaitSingle()
    }
}
```

### Connection Pooling

**Java:**

```java
@Configuration
public class DatabaseConfig {
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:postgresql://localhost:5432/db");
        config.setUsername("user");
        config.setPassword("password");
        config.setMaximumPoolSize(10);

        return new HikariDataSource(config);
    }
}
```

**Kotlin:**

```kotlin
@Configuration
class DatabaseConfig {
    @Bean
    fun dataSource() = HikariDataSource(HikariConfig().apply {
        jdbcUrl = "jdbc:postgresql://localhost:5432/db"
        username = "user"
        password = "password"
        maximumPoolSize = 10
    })
}
```

**Key Differences:**

1. **Basic Connections:**

   - Kotlin's use function for resource management
   - More concise syntax in Kotlin
   - Better null safety in Kotlin
   - Extension functions for JDBC types

2. **Spring Data:**

   - Data classes in Kotlin reduce boilerplate
   - Constructor injection is simpler in Kotlin
   - Properties instead of getters/setters
   - Null safety for database operations

3. **Kotlin-Specific Features:**

   - Exposed DSL for type-safe SQL
   - Coroutine support for async operations
   - Flow API for reactive programming
   - Better connection pooling configuration

4. **Performance:**

   - Both use same underlying JDBC
   - Kotlin coroutines for non-blocking IO
   - Connection pooling works identically
   - Kotlin compiler optimizations

5. **Type Safety:**

   - Kotlin's null safety system
   - Exposed's type-safe DSL
   - Smart casts in Kotlin
   - Better generic support

6. **Modern Features:**
   - Coroutines for async operations
   - Extension functions for utilities
   - DSL capabilities
   - Functional programming support
