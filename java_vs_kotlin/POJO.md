[`<--`](../JavaVsKotlin.md)

## 🧱 POJOs vs Data Classes

### Traditional vs Automatic Implementation

**Java (Traditional POJO):**

```java
public class Product {
    private final Long id;
    private final String name;
    private final BigDecimal price;
    private final String category;

    public Product(Long id, String name, BigDecimal price, String category) {
        this.id = id;
        this.name = name;
        this.price = price;
        this.category = category;
    }

    public Long getId() { return id; }
    public String getName() { return name; }
    public BigDecimal getPrice() { return price; }
    public String getCategory() { return category; }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Product product = (Product) obj;
        return Objects.equals(id, product.id) &&
               Objects.equals(name, product.name) &&
               Objects.equals(price, product.price) &&
               Objects.equals(category, product.category);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name, price, category);
    }

    @Override
    public String toString() {
        return "Product{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", price=" + price +
                ", category='" + category + '\'' +
                '}';
    }
}
```

**Kotlin (Data Class):**

```kotlin
data class Product(
    val id: Long,
    val name: String,
    val price: BigDecimal,
    val category: String
) {
    // equals(), hashCode(), toString(), copy() generated automatically
    // Getters generated automatically (no setters because they're val)
}
```

**Key Differences:**

- A 5-line Kotlin data class replaces 40+ lines of Java
- Kotlin automatically generates `equals()`, `hashCode()`, `toString()`, `copy()` and destructuring
- Properties are immutable by default (`val`)
- Complete elimination of boilerplate

### Practical Usage and Additional Functions

**Java:**

```java
public class ProductService {
    public Product updatePrice(Product original, BigDecimal newPrice) {
        // Creating new instance requires full constructor
        return new Product(
            original.getId(),
            original.getName(),
            newPrice,
            original.getCategory()
        );
    }

    public void printProducts(List<Product> products) {
        for (Product product : products) {
            System.out.println(product.toString());
        }
    }

    public boolean areProductsEqual(Product p1, Product p2) {
        return p1.equals(p2); // Works only if we implement equals correctly
    }
}
```

**Kotlin:**

```kotlin
class ProductService {
    fun updatePrice(original: Product, newPrice: BigDecimal): Product {
        // copy() function generated automatically
        return original.copy(price = newPrice)
    }

    fun printProducts(products: List<Product>) {
        products.forEach { println(it) } // Automatic toString()
    }

    fun areProductsEqual(p1: Product, p2: Product): Boolean {
        return p1 == p2 // Automatic equals() generation
    }

    // Destructuring (unique to Kotlin)
    fun processProduct(product: Product) {
        val (id, name, price, category) = product
        println("Processing $name (ID: $id) in category $category for $price")
    }
}
```

**Data Class Advantages:**

- Drastic reduction in boilerplate code (90% fewer lines)
- `copy()` function for functional immutability
- Destructuring for easy property extraction
- Automatic consistency in `equals()`, `hashCode()`, `toString()`

**Java POJO Advantages:**

- Complete control over method implementation
- Familiarity for teams with Java experience
- Compatibility with tools that analyze traditional Java bytecode

## 🧬 Inheritance and Composition

### Abstract Classes and Interfaces

**Java:**

```java
// Abstract class
public abstract class BaseRepository<T, ID> {
    protected final EntityManager entityManager;

    public BaseRepository(EntityManager entityManager) {
        this.entityManager = entityManager;
    }

    public abstract Class<T> getEntityClass();

    public T findById(ID id) {
        return entityManager.find(getEntityClass(), id);
    }

    public T save(T entity) {
        entityManager.persist(entity);
        return entity;
    }
}

// Interface
public interface Cacheable {
    void clearCache();

    default boolean isCacheEnabled() {
        return true;
    }
}

// Implementation
public class UserRepository extends BaseRepository<User, Long> implements Cacheable {
    private final CacheManager cacheManager;

    public UserRepository(EntityManager entityManager, CacheManager cacheManager) {
        super(entityManager);
        this.cacheManager = cacheManager;
    }

    @Override
    public Class<User> getEntityClass() {
        return User.class;
    }

    @Override
    public void clearCache() {
        cacheManager.clearCache("users");
    }
}
```

**Kotlin:**

```kotlin
// Abstract class
abstract class BaseRepository<T, ID>(
    protected val entityManager: EntityManager
) {
    abstract fun getEntityClass(): KClass<T>

    fun findById(id: ID): T? {
        return entityManager.find(getEntityClass().java, id)
    }

    fun save(entity: T): T {
        entityManager.persist(entity)
        return entity
    }
}

// Interface
interface Cacheable {
    fun clearCache()

    // Method with default implementation
    fun isCacheEnabled(): Boolean = true
}

// Implementation
class UserRepository(
    entityManager: EntityManager,
    private val cacheManager: CacheManager
) : BaseRepository<User, Long>(entityManager), Cacheable {

    override fun getEntityClass(): KClass<User> = User::class

    override fun clearCache() {
        cacheManager.clearCache("users")
    }
}
```

### Delegation

**Java (Manual Decorator Pattern):**

```java
public class CachingUserRepository implements UserRepositoryInterface {
    private final UserRepositoryInterface delegate;
    private final CacheManager cacheManager;

    public CachingUserRepository(UserRepositoryInterface delegate, CacheManager cacheManager) {
        this.delegate = delegate;
        this.cacheManager = cacheManager;
    }

    @Override
    public User findById(Long id) {
        String cacheKey = "user_" + id;
        User cached = cacheManager.get(cacheKey, User.class);
        if (cached != null) {
            return cached;
        }

        User user = delegate.findById(id);
        cacheManager.put(cacheKey, user);
        return user;
    }

    @Override
    public List<User> findAll() {
        // Manual delegation for each method
        return delegate.findAll();
    }

    @Override
    public User save(User user) {
        // Clear cache and delegate
        cacheManager.clearCache("users");
        return delegate.save(user);
    }

    // We need to manually implement all interface methods...
}
```

**Kotlin (Native Delegation):**

```kotlin
class CachingUserRepository(
    private val delegate: UserRepositoryInterface,
    private val cacheManager: CacheManager
) : UserRepositoryInterface by delegate { // Automatic delegation

    // Only override methods that need special behavior
    override fun findById(id: Long): User? {
        val cacheKey = "user_$id"
        val cached = cacheManager.get(cacheKey, User::class.java)
        if (cached != null) {
            return cached
        }

        val user = delegate.findById(id)
        user?.let { cacheManager.put(cacheKey, it) }
        return user
    }

    override fun save(user: User): User {
        cacheManager.clearCache("users")
        return delegate.save(user)
    }

    // All other methods are automatically delegated
    // findAll(), delete(), etc. are handled by 'delegate' automatically
}
```

**Key Differences:**

- Kotlin supports native delegation with `by`, eliminating boilerplate
- Inheritance in Kotlin requires explicit declaration (`open` keyword)
- More concise syntax for constructors and property initialization
- Better safety with classes closed by default
