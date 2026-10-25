# Java Optional

In traditional Java, `null` was the default way to represent the absence of a value. This led to the infamous "Billion-Dollar Mistake"—code littered with defensive `if (x != null)` checks and unexpected `NullPointerException` (NPE) crashes at runtime when a check was forgotten.

**`Optional<T>`** (introduced in Java 8) is a single-value container object that either contains a non-null value or is empty. It is designed primarily as a **method return type** to clearly signal to API callers that a value might be missing, forcing them to handle the empty case safely.

> **Key Rule of Thumb:**  
> `Optional` does **not** eliminate `null` from Java; it eliminates **silent, unexpected `null` returns** by making absence explicit in method signatures.

---

## 1. Why Did We Need This?

### The Old Way: Deep Null Checks & Fragile Chains
```java
// Prone to NullPointerException at any step
public String getUserCity(User user) {
    if (user != null) {
        Address address = user.getAddress();
        if (address != null) {
            City city = address.getCity();
            if (city != null) {
                return city.getName();
            }
        }
    }
    return "Unknown";
}
```

### The New Way: Clean, Expressive Pipeline
```java
// Safe, readable, and zero NullPointerExceptions
public String getUserCity(User user) {
    return Optional.ofNullable(user)
        .map(User::getAddress)
        .map(Address::getCity)
        .map(City::getName)
        .orElse("Unknown");
}
```

---

## 2. How to Create an `Optional`

There are only **three factory methods** to create an `Optional`:

| Method | Behavior | When to Use |
| :--- | :--- | :--- |
| **`Optional.of(value)`** | Wraps a non-null value. **Throws NPE immediately** if value is `null`. | When you are 100% certain the value is never null. |
| **`Optional.ofNullable(value)`** | Returns an `Optional` with the value if present, or `Optional.empty()` if `null`. | When the incoming value might be `null`. |
| **`Optional.empty()`** | Returns an explicitly empty `Optional`. | To represent an empty result without returning `null`. |

```java
Optional<String> opt1 = Optional.of("Hello");          // Contains "Hello"
Optional<String> opt2 = Optional.ofNullable(null);     // Equivalent to Optional.empty()
Optional<String> opt3 = Optional.empty();              // Empty container
// Optional.of(null);                                  // ❌ Throws NullPointerException!
```

---

## 3. How to Consume an `Optional` (The Good vs. The Bad)

### ❌ The Anti-Pattern (Don't Write Old Code with New Syntax)
```java
// ❌ BAD: This is just verbose "if (val != null)" in disguise!
if (optUser.isPresent()) {
    System.out.println(optUser.get().getName());
}
```

### ✅ The Modern, Idiomatic Way

#### 1. Provide a Fallback Value: `orElse()` vs. `orElseGet()`
```java
// orElse(): Simple fallback constant
String name = optName.orElse("Guest");

// orElseGet(): Supplier evaluated LAZILY (only if optional is empty)
String token = optToken.orElseGet(() -> generateExpensiveToken());
```

#### 2. Throw an Exception: `orElseThrow()`
```java
// Throws NoSuchElementException (Java 10+)
User user = userRepository.findById(id).orElseThrow();

// Throws Custom Business Exception
User user = userRepository.findById(id)
    .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
```

#### 3. Execute Actions on Presence: `ifPresent()` and `ifPresentOrElse()`
```java
// Runs only if value is present
optUser.ifPresent(user -> emailService.sendWelcome(user));

// Java 9: Handles both present and empty branches cleanly
optUser.ifPresentOrElse(
    user -> emailService.sendWelcome(user),
    () -> logger.warn("No user found")
);
```

#### 4. Transform and Filter: `map()`, `flatMap()`, and `filter()`
```java
Optional<String> validEmail = optUser
    .map(User::getEmail)                // Extracts email (1-to-1)
    .filter(email -> email.contains("@")); // Keeps value only if condition matches
```

---

## 4. `orElse()` vs. `orElseGet()` (The #1 Interview Trap)

This is one of the most frequently asked Java interview questions.

| Feature | `orElse(value)` | `orElseGet(Supplier)` |
| :--- | :--- | :--- |
| **Evaluation Timing** | **Eager** (Runs every time) | **Lazy** (Runs ONLY if optional is empty) |
| **Performance Impact** | Can cause unnecessary DB queries or object creation | High performance (no wasted computations) |
| **Best Used For** | Pre-existing constants or cheap defaults (`"Guest"`, `0`) | Database queries, API calls, or expensive allocations |

### ⚠️ Dangerous Example:
```java
// Assume optUser has a value:
User user1 = optUser.orElse(createDefaultUserInDatabase());    
// ⚠️ DANGER: createDefaultUserInDatabase() RUNS ANYWAY and inserts a duplicate user!

User user2 = optUser.orElseGet(() -> createDefaultUserInDatabase()); 
// ✅ SAFE: Lambda is NEVER called because optUser is already present!
```

---

## 5. `map()` vs. `flatMap()` in `Optional`

* **`map(Function)`:** Use when the transformation method returns a **regular object**.
* **`flatMap(Function)`:** Use when the transformation method **already returns an `Optional`** (prevents `Optional<Optional<T>>`).

```java
public class User {
    // Getter returns an Optional
    public Optional<Address> getAddress() { ... }
}

Optional<User> optUser = Optional.of(new User());

// ❌ map() creates nested Optional: Optional<Optional<Address>>
Optional<Optional<Address>> nested = optUser.map(User::getAddress);

// ✅ flatMap() unwraps and flattens it: Optional<Address>
Optional<Address> address = optUser.flatMap(User::getAddress);
```

---

## 6. What NOT to Do with `Optional` (Best Practice Rules)

### 1. Never use `Optional` as a Class Field
* `Optional` does **not** implement `java.io.Serializable`.
* Storing `Optional` fields breaks standard Java serialization, frameworks like Hibernate/JPA, and increases memory footprint (each `Optional` is an extra heap object).
* **Fix:** Use regular nullable fields and let getters return `Optional` if desired.

### 2. Never use `Optional` as a Method Parameter
```java
// ❌ BAD: Forces caller to wrap arguments: findUser("alex", Optional.of(10))
public void findUser(String name, Optional<Integer> age) { ... }

// ✅ GOOD: Use method overloading or standard null checks
public void findUser(String name, Integer age) { ... }
public void findUser(String name) { findUser(name, null); }
```

### 3. Never wrap Collections in an `Optional`
* An empty collection (`List.of()`, `Collections.emptyList()`) already represents the absence of items cleanly.
* `Optional<List<T>>` introduces confusing two-level checks: is the optional empty, or is the list inside empty?
* **Rule:** Return an empty `List`, `Set`, or `Map`, never an `Optional<List<T>>`.

### 4. Never call `.get()` blindly
* Calling `.get()` on an empty `Optional` throws `NoSuchElementException`.
* Always prefer `orElse()`, `orElseGet()`, `orElseThrow()`, or `ifPresent()`.

---

## 7. The 5 Traps for Interviews

### Trap 1: Eager execution bug with `orElse()`
* **Question:** *"Why does my logging/database method run even when the Optional has a value?"*
* **Answer:** You used `orElse(expensiveMethod())` instead of `orElseGet(() -> expensiveMethod())`. Arguments in Java methods are evaluated before the method call executes.

---

### Trap 2: Returning `null` from an `Optional`-returning method
* **Problem:** `public Optional<User> findUser() { return null; }`
* **Why it's awful:** Defeats the entire purpose of `Optional` and leads to `NullPointerException` on the caller side (`findUser().isPresent()`).
* **Rule:** **Always return `Optional.empty()`, NEVER return `null`!**

---

### Trap 3: Memory overhead in high-throughput loops
* Each `Optional` is an object allocated on the heap (~16 to 24 bytes).
* In high-throughput, low-latency critical loops or primitive collections, avoid wrapping millions of elements in `Optional`. Use primitives with sentinel values or primitive optionals (`OptionalInt`, `OptionalLong`, `OptionalDouble`).

---

### Trap 4: Serializing `Optional` with Jackson or JPA
* Jackson requires the `jackson-datatype-jdk8` module to serialize/deserialize `Optional` properly.
* JPA specifications do not support `Optional` fields for entity mapping.

---

### Trap 5: Replacing simple local variables with `Optional`
* `Optional` is meant as an API return type, not for micro-optimizing every local `if (x != null)` check inside a private helper method.

---

## 8. How to Answer in an Interview (Exact Wordings)

### Q1: "What is `Optional` in Java and why was it introduced?"
> *"Optional is a container object introduced in Java 8 that may or may not contain a non-null value. It was introduced primarily as a method return type to clearly communicate to callers that a result might be absent, eliminating silent NullPointerExceptions and replacing defensive null checks with expressive, functional pipelines."*

### Q2: "What is the difference between `orElse()` and `orElseGet()`?"
> *"The key difference is evaluation timing. `orElse()` evaluates its fallback argument eagerly—meaning it executes regardless of whether the Optional is empty or present. `orElseGet()` takes a Supplier and evaluates lazily—meaning the code inside only runs if the Optional is actually empty. Therefore, for expensive operations or database calls, `orElseGet()` must be used."*

### Q3: "Should we use `Optional` for class fields or method parameters?"
> *"No. Optional was specifically designed as a return type. It does not implement Serializable, so using it for fields breaks Java and JPA serialization and adds heap overhead. Using it as a method parameter creates unnecessary ceremony for the caller; method overloading or standard null handling is preferred."*

### Q4: "What is the difference between `Optional.map()` and `Optional.flatMap()`?"
> *"Both transform the value inside the Optional if present. Use `map` when the mapping function returns a regular value. Use `flatMap` when the mapping function itself returns an `Optional`, preventing the creation of nested `Optional<Optional<T>>` structures."*

---

## 9. 60-Second Recall Checklist

1. **Purpose:** Designed as a **method return type** to explicitly signal possible absence of a value.
2. **Creation:** `Optional.of(val)` (non-null), `Optional.ofNullable(val)` (safe for null), `Optional.empty()`.
3. **`orElse` vs `orElseGet`:** `orElse` is **eager**; `orElseGet` is **lazy** (use for heavy operations).
4. **Never Return `null`:** Always return `Optional.empty()` from an Optional method.
5. **No `Optional` Fields:** Not `Serializable`; adds heap overhead.
6. **No `Optional<List<T>>`:** Return an empty collection instead.
7. **Idiomatic Flow:** Prefer `map()`, `filter()`, `orElseGet()`, `ifPresent()` over `isPresent() + get()`.
8. **Primitive Variants:** Use `OptionalInt`, `OptionalLong`, `OptionalDouble` to avoid boxing overhead.
