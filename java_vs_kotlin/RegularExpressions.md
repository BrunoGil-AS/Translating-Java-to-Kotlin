# [`<— Complete Java vs Kotlin Backend Comparison`](../README.md)

## 📝 Regular Expressions

### Basic Pattern Matching

**Java:**

```java
public class RegexExample {
    public boolean isValidEmail(String email) {
        Pattern pattern = Pattern.compile("^[A-Za-z0-9+_.-]+@(.+)$");
        Matcher matcher = pattern.matcher(email);
        return matcher.matches();
    }

    public String replaceSpaces(String text) {
        return text.replaceAll("\\s+", "-");
    }
}
```

**Kotlin:**

```kotlin
class RegexExample {
    // Regex can be stored as a property
    private val emailRegex = Regex("^[A-Za-z0-9+_.-]+@(.+)$")

    fun isValidEmail(email: String) = emailRegex.matches(email)

    fun replaceSpaces(text: String) = text.replace("\\s+".toRegex(), "-")
}
```

### Advanced Pattern Matching

**Java:**

```java
public class AdvancedRegex {
    public List<String> extractDates(String text) {
        Pattern pattern = Pattern.compile("\\d{2}/\\d{2}/\\d{4}");
        Matcher matcher = pattern.matcher(text);
        List<String> dates = new ArrayList<>();

        while (matcher.find()) {
            dates.add(matcher.group());
        }

        return dates;
    }
}
```

**Kotlin:**

```kotlin
class AdvancedRegex {
    fun extractDates(text: String): List<String> {
        val regex = "\\d{2}/\\d{2}/\\d{4}".toRegex()
        return regex.findAll(text)
            .map { it.value }
            .toList()
    }

    fun extractGroups(text: String) {
        val pattern = """(\d{2})/(\d{2})/(\d{4})""".toRegex()

        pattern.findAll(text).forEach { matchResult ->
            val (day, month, year) = matchResult.destructured
            println("Day: $day, Month: $month, Year: $year")
        }
    }
}
```

### String Processing with Regex

**Java:**

```java
public class StringProcessor {
    public String maskCreditCard(String cardNumber) {
        return cardNumber.replaceAll("\\d(?=\\d{4})", "*");
    }

    public boolean isStrongPassword(String password) {
        String pattern = "^(?=.*[A-Z])(?=.*[!@#$&*])(?=.*[0-9])(?=.*[a-z]).{8,}$";
        return Pattern.matches(pattern, password);
    }
}
```

**Kotlin:**

```kotlin
class StringProcessor {
    fun maskCreditCard(cardNumber: String) =
        cardNumber.replace("\\d(?=\\d{4})".toRegex(), "*")

    fun isStrongPassword(password: String) =
        password.matches("""
            ^              # start of line
            (?=.*[A-Z])   # at least one uppercase
            (?=.*[!@#$&*])# at least one special char
            (?=.*[0-9])   # at least one digit
            (?=.*[a-z])   # at least one lowercase
            .{8,}         # at least 8 chars
            $            # end of line
        """.trimIndent().toRegex(RegexOption.COMMENTS))
}
```

**Key Differences:**

- Kotlin provides a dedicated `Regex` class with simpler syntax
- Raw strings (`"""`) make complex patterns more readable
- Extension functions make regex operations more intuitive
- Built-in destructuring for group matching
- More functional approach with sequences (`findAll`)
- RegexOption for pattern flexibility
