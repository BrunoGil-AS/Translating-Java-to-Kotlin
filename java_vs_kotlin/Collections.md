# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 🧰 Collections and Streams

### Kotlin vs Java Collections

![alt text](../resources/CollectionsDiagram.png)

| **Kotlin Collection**    | **Mutable?** | **Java Equivalent**                | **Notes**                                                                                             |
| ------------------------ | ------------ | ---------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `List<T>`                | ❌ Immutable | `java.util.List` (read-only view)  | Read-only view in Kotlin, but backed by a Java `List`. Not truly immutable.                           |
| `MutableList<T>`         | ✅ Mutable   | `java.util.List` / `ArrayList`     | Can add/remove elements. Typically backed by `ArrayList`.                                             |
| `Set<T>`                 | ❌ Immutable | `java.util.Set` (read-only view)   | Read-only view, backed by a Java `Set`.                                                               |
| `MutableSet<T>`          | ✅ Mutable   | `java.util.Set` / `HashSet`        | Backed usually by `HashSet`.                                                                          |
| `Map<K, V>`              | ❌ Immutable | `java.util.Map` (read-only view)   | Read-only view, backed by Java `Map`.                                                                 |
| `MutableMap<K, V>`       | ✅ Mutable   | `java.util.Map` / `HashMap`        | Can put/remove key-value pairs.                                                                       |
| `Array<T>`               | ✅ Mutable   | `T[]` (Java arrays)                | Fixed-size, mutable array. Unlike `List`, supports primitive arrays (`IntArray`, `DoubleArray`, etc). |
| `IntArray`, `LongArray`… | ✅ Mutable   | `int[]`, `long[]`…                 | Specialized primitive arrays to avoid boxing.                                                         |
| `Sequence<T>`            | ❌ Immutable | `java.util.stream.Stream<T>`       | Lazy, one-pass iteration (similar to Java streams).                                                   |
| `Iterable<T>`            | ❌ Immutable | `java.lang.Iterable<T>`            | Base type for collections, just like in Java.                                                         |
| `MutableIterable<T>`     | ✅ Mutable   | `java.lang.Iterable<T>` + mutation | Can remove via iterator.                                                                              |

#### **Key Differences**

- Kotlin makes a **clear distinction** between **read-only (`List`, `Set`, `Map`)** and **mutable (`MutableList`, `MutableSet`, `MutableMap`)** collections.  
  In Java, mutability is implicit depending on the implementation (`List` could be backed by `ArrayList` or `Collections.unmodifiableList`).
- Kotlin `List`, `Set`, and `Map` are **interfaces**, not truly immutable; they just **don’t expose mutating methods**.
- Kotlin provides **primitive arrays** (`IntArray`, `DoubleArray`, etc.) for performance, which Java handles with native arrays.
- Kotlin `Sequence` is lazy like Java `Stream`, but simpler and without parallel execution support.

---

### List, Set, Map

**Java:**

```java
import java.util.*;
import java.util.stream.Collectors;

public class CollectionExamples {
    public void demonstrateCollections() {
        // List
        List<String> languageList = new ArrayList<>();
        languageList.add("Java");
        languageList.add("Kotlin");
        languageList.add("Java");
        System.out.println("List: " + languageList); // [Java, Kotlin, Java]

        // Set
        Set<String> languageSet = new HashSet<>();
        languageSet.add("Java");
        languageSet.add("Kotlin");
        languageSet.add("Java"); // Duplicate is ignored
        System.out.println("Set: " + languageSet); // [Java, Kotlin]

        // Map
        Map<String, Integer> languageRanking = new HashMap<>();
        languageRanking.put("Java", 2);
        languageRanking.put("Kotlin", 1);
        System.out.println("Map: " + languageRanking); // {Java=2, Kotlin=1}

        // Immutable collections (since Java 9)
        List<String> immutableList = List.of("Go", "Rust");
        // immutableList.add("C++"); // Throws UnsupportedOperationException
    }
}
```

**Kotlin:**

```kotlin
fun demonstrateCollections() {
    // List (immutable by default)
    val languageList = listOf("Kotlin", "Java", "Kotlin")
    println("Immutable List: $languageList") // [Kotlin, Java, Kotlin]

    // Mutable List
    val mutableLanguageList = mutableListOf("Kotlin", "Java")
    mutableLanguageList.add("Go")
    println("Mutable List: $mutableLanguageList") // [Kotlin, Java, Go]

    // Set (immutable by default)
    val languageSet = setOf("Kotlin", "Java", "Kotlin") // Duplicates ignored
    println("Immutable Set: $languageSet") // [Kotlin, Java]

    // Map (immutable by default)
    val languageRanking = mapOf("Kotlin" to 1, "Java" to 2)
    println("Immutable Map: $languageRanking") // {Kotlin=1, Java=2}

    // Mutable Map
    val mutableRanking = mutableMapOf("Kotlin" to 1, "Java" to 2)
    mutableRanking["Go"] = 3
    println("Mutable Map: $mutableRanking") // {Kotlin=1, Java=2, Go=3}
}
```

#### Common methods

- Kotlin collections are much more concise, with operators like [], in, +, union, intersect.

- Most “stream” operations in Java (map, filter, groupingBy) are direct methods in Kotlin collections.

- Read-only (List, Set, Map) vs mutable (MutableList, MutableSet, MutableMap) distinction is unique to Kotlin.

##### 📋 Lists

| **Operation**       | **Kotlin (List / MutableList)**     | **Java (List / ArrayList, Streams)**           | **Notes**                                |
| ------------------- | ----------------------------------- | ---------------------------------------------- | ---------------------------------------- |
| Create list         | `val list = listOf(1, 2, 3)`        | `List<Integer> list = Arrays.asList(1, 2, 3);` | Kotlin `listOf` is read-only by default. |
| Create mutable list | `val list = mutableListOf(1, 2, 3)` | `List<Integer> list = new ArrayList<>(...)`    | Fully mutable.                           |
| Access element      | `list[0]`                           | `list.get(0)`                                  | Kotlin uses `[]` operator.               |
| Add element         | `list.add(4)`                       | `list.add(4)`                                  | Same in mutable lists.                   |
| Remove element      | `list.remove(2)`                    | `list.remove(Integer.valueOf(2))`              | Simpler in Kotlin.                       |
| Iterate             | `for (x in list) { ... }`           | `for (Integer x : list) { ... }`               | Cleaner syntax.                          |
| Map elements        | `list.map { it * 2 }`               | `list.stream().map(x -> x * 2).toList()`       | Kotlin much shorter.                     |
| Filter elements     | `list.filter { it > 2 }`            | `list.stream().filter(x -> x > 2).toList()`    | Same semantics.                          |
| Sort                | `list.sorted()` / `sortedBy { it }` | `Collections.sort(list)` / streams             | Kotlin more expressive.                  |
| Distinct            | `list.distinct()`                   | `list.stream().distinct().toList()`            | Direct helper.                           |

##### 🔀 Sets

| **Operation**      | **Kotlin (Set / MutableSet)**     | **Java (Set / HashSet, Streams)**                                         | **Notes**                 |
| ------------------ | --------------------------------- | ------------------------------------------------------------------------- | ------------------------- |
| Create set         | `val set = setOf(1, 2, 3)`        | `Set<Integer> set = new HashSet<>(Arrays.asList(1,2,3));`                 | Immutable in Kotlin.      |
| Create mutable set | `val set = mutableSetOf(1, 2, 3)` | `Set<Integer> set = new HashSet<>();`                                     | Fully mutable.            |
| Add element        | `set.add(4)`                      | `set.add(4)`                                                              | Same.                     |
| Remove element     | `set.remove(2)`                   | `set.remove(2)`                                                           | Same.                     |
| Contains element   | `2 in set`                        | `set.contains(2)`                                                         | Kotlin has `in` operator. |
| Union              | `set1 union set2`                 | `Stream.concat(set1.stream(), set2.stream()).collect(Collectors.toSet())` | Operator style in Kotlin. |
| Intersect          | `set1 intersect set2`             | `set1.retainAll(set2)` or Streams                                         | Much cleaner in Kotlin.   |
| Subtract           | `set1 subtract set2`              | `set1.removeAll(set2)`                                                    | Direct in Kotlin.         |
| Iterate            | `for (x in set)`                  | `for (Integer x : set)`                                                   | Similar.                  |

##### 🗺️ Maps

| **Operation**       | **Kotlin (Map / MutableMap)**            | **Java (Map / HashMap, Streams)**                 | **Notes**                                |
| ------------------- | ---------------------------------------- | ------------------------------------------------- | ---------------------------------------- |
| Create map          | `val map = mapOf(1 to "a", 2 to "b")`    | `Map<Integer, String> map = Map.of(1,"a",2,"b");` | `to` is Kotlin’s infix pair constructor. |
| Create mutable map  | `val map = mutableMapOf(1 to "a")`       | `Map<Integer,String> map = new HashMap<>();`      | Fully mutable.                           |
| Get value           | `map[1]`                                 | `map.get(1)`                                      | Kotlin uses `[]` operator.               |
| Put value           | `map[3] = "c"`                           | `map.put(3, "c")`                                 | Cleaner in Kotlin.                       |
| Remove key          | `map.remove(2)`                          | `map.remove(2)`                                   | Same.                                    |
| Iterate keys/values | `for ((k,v) in map) { ... }`             | `for (Map.Entry<K,V> entry : map.entrySet())`     | Destructuring in Kotlin.                 |
| Keys only           | `map.keys`                               | `map.keySet()`                                    | Property vs method.                      |
| Values only         | `map.values`                             | `map.values()`                                    | Same.                                    |
| Filter by key/value | `map.filter { it.key > 1 }`              | `map.entrySet().stream().filter(...).toMap()`     | Kotlin simpler.                          |
| Transform values    | `map.mapValues { it.value.uppercase() }` | `map.replaceAll((k,v) -> v.toUpperCase())`        | Kotlin returns new map by default.       |
| Merge / combine     | `map1 + map2`                            | `map1.putAll(map2)`                               | `+` operator in Kotlin.                  |

// TODO add callback.

---

### Java Streams vs Kotlin Sequences

**Java Streams:**

- **Lazy evaluation**: Operations on a stream are divided into intermediate (e.g., `filter`, `map`) and terminal (e.g., `collect`).
- Intermediate operations are not executed until a terminal operation is invoked.

```java
// Example showing Java Stream's lazy evaluation
public class StreamExample {
    public void processNumbers() {
        // Create source collection
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Build processing pipeline with debug output
        List<Integer> result = numbers.stream()
            .filter(n -> {
                // This won't execute until collect() is called
                System.out.println("Filtering: " + n);
                return n % 2 == 0;
            })
            .map(n -> {
                // Transform after filtering
                System.out.println("Mapping: " + n);
                return n * 2;
            })
            .limit(3)  // Take only first 3 results
            .collect(Collectors.toList());

        System.out.println("Result: " + result);
    }
}
```

**Kotlin Sequences:**

- Similar to Java Streams, Sequences provide lazy evaluation for complex data processing pipelines.
- For large collections, `asSequence()` is more performant than direct collection operations as it avoids creating intermediate collections.

```kotlin
// Kotlin Sequence example with similar lazy evaluation
fun processNumbers() {
    // Create number range and convert to list
    val numbers = (1..10).toList()

    // Convert to sequence for lazy evaluation
    val result = numbers.asSequence()
        .filter {
            // Shows when filter is actually executed
            println("Filtering: $it")
            it % 2 == 0
        }
        .map {
            // Shows when map is executed
            println("Mapping: $it")
            it * 2
        }
        .take(3)     // Similar to limit() in Java
        .toList()    // Terminal operation

    println("Result: $result")
}
```

**Key Differences:**

- Kotlin Sequences are simpler to create with `asSequence()`
- More concise syntax with lambda expressions
- Built-in range support (1..10)
- Terminal operations like `toList()` are clearer
- Natural string interpolation for debugging

### Functional operations: map, filter, reduce

**Java:**

```java
// Java functional operations example
public class FunctionalOperations {
    public void demonstrate() {
        // Source collection
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // Transform using map operation
        List<Integer> squared = numbers.stream()
            .map(n -> n * n)             // Square each number
            .collect(Collectors.toList()); // [1, 4, 9, 16, 25]

        // Filter operation for even numbers
        List<Integer> evenNumbers = numbers.stream()
            .filter(n -> n % 2 == 0)      // Keep only even numbers
            .collect(Collectors.toList()); // [2, 4]

        // Reduce operation to sum all numbers
        int sum = numbers.stream()
            .reduce(0, Integer::sum);     // 15 (method reference)
    }
}
```

**Kotlin:**

```kotlin
// Kotlin functional operations example
fun demonstrate() {
    // Create list using factory function
    val numbers = listOf(1, 2, 3, 4, 5)

    // Direct map operation on collection
    val squared = numbers.map { it * it }          // [1, 4, 9, 16, 25]

    // Filter using predicate lambda
    val evenNumbers = numbers.filter { it % 2 == 0 } // [2, 4]

    // Reduce operation with lambda
    val sum = numbers.reduce { acc, n -> acc + n }   // 15

    // Fold with initial value
    val sumWithInitial = numbers.fold(0) { acc, n -> acc + n } // 15
}
```

**Key Differences:**

- Kotlin operations work directly on collections
- No need for stream conversion or collection
- More concise lambda syntax with 'it'
- Built-in fold operation with initial value
- No explicit type declarations needed

Perfect 👍 Let’s go step by step. I’ll teach you **arrays in Kotlin** and compare them directly with **Java arrays**, since the two languages treat arrays differently.

---

## 📦 Arrays in Kotlin vs Java

| **Feature**          | **Java**                                                                                   | **Kotlin**                                                                                 |
| -------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| **Declaration**      | Arrays are created with `new` or array literals.                                           | Arrays are created with factory functions (`arrayOf`, `intArrayOf`, etc.).                 |
| **Fixed size**       | Yes – array length is fixed once created.                                                  | Yes – array size is fixed, but Kotlin encourages use of `List`/`MutableList` instead.      |
| **Access**           | Index-based, `arr[0]`.                                                                     | Same: `arr[0]`.                                                                            |
| **Length**           | `.length`                                                                                  | `.size`                                                                                    |
| **Printing**         | `System.out.println(arr)` → prints object ref (use `Arrays.toString(arr)`).                | `println(arr)` prints memory ref too, but use `arr.contentToString()` for readable output. |
| **Type safety**      | Arrays are **covariant** (`String[]` is subtype of `Object[]`) → can cause runtime errors. | Kotlin arrays are **invariant**, safer at compile time.                                    |
| **Primitive arrays** | Java has primitive arrays (`int[]`, `double[]`).                                           | Kotlin has specialized arrays (`IntArray`, `DoubleArray`) that avoid boxing overhead.      |
| **Iteration**        | For-each loop: `for (int x : arr)`.                                                        | For-each loop: `for (x in arr)`.                                                           |

---

### 🔹 Examples

#### 1. Declaring and Initializing Arrays

**Java:**

```java
// Declaration
int[] numbers = new int[3];  // default 0 values
numbers[0] = 10;
numbers[1] = 20;
numbers[2] = 30;

// Array literal
String[] names = {"Alice", "Bob", "Charlie"};
```

**Kotlin:**

```kotlin
// Declaration
val numbers = arrayOf(10, 20, 30)   // Inferred as Array<Int>

// Primitive array (no boxing)
val numbers2 = intArrayOf(10, 20, 30)

// String array
val names = arrayOf("Alice", "Bob", "Charlie")
```

---

#### 2. Access and Properties

**Java:**

```java
System.out.println(numbers[0]);  // 10
System.out.println(numbers.length);  // 3
```

**Kotlin:**

```kotlin
println(numbers[0])   // 10
println(numbers.size) // 3
```

---

#### 3. Iteration

**Java:**

```java
for (int n : numbers) {
    System.out.println(n);
}
```

**Kotlin:**

```kotlin
for (n in numbers) {
    println(n)
}
```

---

#### 4. Printing Arrays

**Java:**

```java
System.out.println(Arrays.toString(numbers)); // [10, 20, 30]
```

**Kotlin:**

```kotlin
println(numbers.contentToString()) // [10, 20, 30]
```

---

#### 5.🏗️ Filling Arrays

**In Java**, you often create an empty array with a fixed size, then assign values:

```java
int[] numbers = new int[5];   // [0, 0, 0, 0, 0]
numbers[0] = 10;
numbers[1] = 20;
numbers[2] = 30;
```

Or use `Arrays.fill`:

```java
int[] numbers = new int[5];
Arrays.fill(numbers, 7); // [7, 7, 7, 7, 7]
```

**Kotlin** doesn’t use `new` — you create arrays with factory functions.

**_Empty Array with Default Values_**

```kotlin
val numbers = Array(5) { 0 }  // size 5, all initialized to 0
println(numbers.contentToString()) // [0, 0, 0, 0, 0]
```

Here:

- `Array(size) { initializer }`
- The lambda `{ 0 }` defines what each element will be.

Equivalent to Java’s `new int[5]`.

**_Fill with a Constant_**

```kotlin
val sevens = Array(5) { 7 }
println(sevens.contentToString()) // [7, 7, 7, 7, 7]
```

**_Fill Based on Index_**

```kotlin
val squares = Array(5) { i -> i * i }
println(squares.contentToString()) // [0, 1, 4, 9, 16]
```

**_Specialized Primitive Arrays_**

Kotlin has special types to avoid boxing (`IntArray`, `DoubleArray`, etc.):

```kotlin
val numbers = IntArray(5) { 0 }   // [0, 0, 0, 0, 0]
val evens = IntArray(5) { it * 2 } // [0, 2, 4, 6, 8]
```

---

**_✅ Summary_**

| **Task**                  | **Java**                                   | **Kotlin**                               |
| ------------------------- | ------------------------------------------ | ---------------------------------------- |
| Empty int array of size 5 | `int[] arr = new int[5];` (defaults to 0s) | `val arr = IntArray(5)` (defaults to 0s) |
| Fill with constant        | `Arrays.fill(arr, 7);`                     | `val arr = Array(5) { 7 }`               |
| Fill with formula/index   | Loop or `IntStream.range...`               | `val arr = Array(5) { i -> i * i }`      |
| Length/Size               | `arr.length`                               | `arr.size`                               |

---

#### 6. Higher-Order Operations (Kotlin advantage)

**Java:**

```java
// Java 8+ Streams
int sum = Arrays.stream(numbers).sum();
```

**Kotlin:**

```kotlin
val sum = numbers.sum()  // Built-in
val doubled = numbers.map { it * 2 }
println(doubled) // [20, 40, 60]
```

---

✅ **Summary**

- Both Java and Kotlin have **fixed-size arrays**, but Kotlin encourages using `List`/`MutableList` for most cases.
- Kotlin has **safer, invariant arrays** and built-in support for **higher-order functions** (`map`, `filter`, `sum`, etc.).
- Printing arrays in Kotlin is much easier (`contentToString()`).

---
