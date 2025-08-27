# [`<— Complete Java vs Kotlin Backend Comparison`](../JavaVsKotlin.md)

## 🗄️ Database Connections

### JDBC Connection

#### Java (JDBC)

```java
public class DatabaseConnection {
    private static final String URL = "jdbc:postgresql://localhost:5432/mydb";
    private static final String USER = "user";
    private static final String PASSWORD = "password";

    public Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }

    public void executeQuery() {
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?")) {

            stmt.setLong(1, 123L);
            ResultSet rs = stmt.executeQuery();

            while (rs.next()) {
                String name = rs.getString("name");
                System.out.println(name);
            }
        } catch (SQLException e) {
            throw new RuntimeException("Database error", e);
        }
    }

    public void executeTransaction() throws SQLException {
        Connection conn = getConnection();
        try {
            conn.setAutoCommit(false);

            // Multiple operations
            PreparedStatement stmt1 = conn.prepareStatement("INSERT INTO...");
            PreparedStatement stmt2 = conn.prepareStatement("UPDATE...");

            stmt1.executeUpdate();
            stmt2.executeUpdate();

            conn.commit();
        } catch (SQLException e) {
            conn.rollback();
            throw e;
        } finally {
            conn.close();
        }
    }
}
```

#### Kotlin (JDBC)

```kotlin
class DatabaseConnection(
    private val url: String = "jdbc:postgresql://localhost:5432/mydb",
    private val user: String = "user",
    private val password: String = "password"
) {
    // Extension function para Connection
    private fun Connection.execute(sql: String, params: List<Any> = emptyList()): ResultSet {
        prepareStatement(sql).use { stmt ->
            params.forEachIndexed { index, param ->
                stmt.setObject(index + 1, param)
            }
            return stmt.executeQuery()
        }
    }

    // Using kotlin.Result for error handling
    fun getConnection(): Result<Connection> = runCatching {
        DriverManager.getConnection(url, user, password)
    }

    fun executeQuery() = runCatching {
        getConnection().getOrThrow().use { conn ->
            conn.execute("SELECT * FROM users WHERE id = ?", listOf(123L)).use { rs ->
                generateSequence {
                    if (rs.next()) rs.getString("name") else null
                }.toList()
            }
        }
    }

    fun executeTransaction() = runCatching {
        getConnection().getOrThrow().use { conn ->
            conn.autoCommit = false

            try {
                // Multiple operations using extension function
                conn.execute("INSERT INTO...", listOf(/* params */))
                conn.execute("UPDATE...", listOf(/* params */))

                conn.commit()
            } catch (e: SQLException) {
                conn.rollback()
                throw e
            }
        }
    }
}
```

### Spring Data JPA

#### Java (Spring)

```java
// Entity
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // Getters, setters, constructors
}

// Repository
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    List<User> findByNameContaining(String name);

    @Query("SELECT u FROM User u WHERE u.email LIKE :pattern")
    List<User> findByEmailPattern(@Param("pattern") String pattern);
}

// Service
@Service
@Transactional
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User createUser(User user) {
        return userRepository.save(user);
    }

    public Optional<User> findUser(Long id) {
        return userRepository.findById(id);
    }

    @Transactional(readOnly = true)
    public List<User> searchUsers(String namePattern) {
        return userRepository.findByNameContaining(namePattern);
    }
}
```

#### Kotlin (Spring)

```kotlin
// Entity
@Entity
@Table(name = "users")
data class User(
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long? = null,

    val name: String,
    val email: String
)

// Repository
@Repository
interface UserRepository : JpaRepository<User, Long> {
    fun findByEmail(email: String): User?
    fun findByNameContaining(name: String): List<User>

    @Query("SELECT u FROM User u WHERE u.email LIKE :pattern")
    fun findByEmailPattern(@Param("pattern") pattern: String): List<User>
}

// Service
@Service
@Transactional
class UserService(
    private val userRepository: UserRepository // Constructor injection
) {
    fun createUser(user: User): User = userRepository.save(user)

    fun findUser(id: Long): User? = userRepository.findById(id).orElse(null)

    @Transactional(readOnly = true)
    fun searchUsers(namePattern: String): List<User> =
        userRepository.findByNameContaining(namePattern)

    // Extension function para UserRepository
    fun UserRepository.findByEmailOrNull(email: String): User? =
        runCatching { findByEmail(email) }.getOrNull()
}
```

### Connection Pooling (HikariCP)

#### Java Configuration

```java
@Configuration
public class DataSourceConfig {
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:postgresql://localhost:5432/mydb");
        config.setUsername("user");
        config.setPassword("password");
        config.setMaximumPoolSize(10);
        config.setMinimumIdle(5);
        config.setIdleTimeout(300000);

        return new HikariDataSource(config);
    }
}
```

#### Kotlin Configuration

```kotlin
@Configuration
class DataSourceConfig {
    @Bean
    fun dataSource(): DataSource = HikariDataSource(
        HikariConfig().apply {
            jdbcUrl = "jdbc:postgresql://localhost:5432/mydb"
            username = "user"
            password = "password"
            maximumPoolSize = 10
            minimumIdle = 5
            idleTimeout = 300000
        }
    )
}
```

**Key Differences:**

1. **Sintaxis y Concisión**

   - Kotlin elimina la necesidad de getters/setters
   - Data classes reducen boilerplate
   - Constructor injection más limpio
   - Apply function para configuración

2. **Null Safety**

   - Kotlin usa tipos nullables explícitos
   - No necesita Optional con tipos nullables
   - Mejor manejo de valores nulos en queries

3. **Gestión de Recursos**

   - Kotlin use function para AutoCloseable
   - Extension functions para operaciones comunes
   - Result type para manejo de errores

4. **Funciones de Extensión**

   - Kotlin permite extender clases existentes
   - Mejor organización de funcionalidad relacionada
   - Más fácil de probar y mantener

5. **Configuración**

   - Kotlin properties vs Java fields
   - DSL-style configuration
   - Menos ceremonial en general

6. **Interoperabilidad**

   - Kotlin funciona perfectamente con Spring
   - Anotaciones funcionan igual
   - JPA funciona sin cambios

7. **Manejo de Transacciones**
   - Similar en ambos lenguajes
   - Anotaciones funcionan igual
   - Kotlin más expresivo en bloques de código
