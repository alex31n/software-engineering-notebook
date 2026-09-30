# Java Records

In traditional Java, writing a simple class to hold data requires dozens of lines of repetitive boilerplate: `private final` fields, constructors, getters, `equals()`, `hashCode()`, and `toString()`. Developers often resorted to libraries like Lombok or IDE auto-generation just to carry a few values.

A **Record** in Java is a special kind of class designed to act as a transparent, immutable carrier for data. It dramatically eliminates boilerplate code by generating fields, constructors, accessors, and utility methods directly from a concise declaration. Introduced as a preview in Java 14 and made a permanent feature in Java 16.

---

## 2. Why Did We Need This?

Before Java 16, creating a simple `User` data object required painful amounts of boilerplate:

```java
// The Old Way: 40+ lines of code just to hold 2 fields
public final class User {
    private final String username;
    private final String email;

    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }

    public String getUsername() { return username; }
    public String getEmail() { return email; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user = (User) o;
        return Objects.equals(username, user.username) && Objects.equals(email, user.email);
    }

    @Override
    public int hashCode() {
        return Objects.hash(username, email);
    }

    @Override
    public String toString() {
        return "User{" + "username='" + username + '\'' + ", email='" + email + '\'' + '}';
    }
}
```

Instead of writing private fields, constructors, getters, and standard Object methods manually, you declare a record in a single line:

```java
// New clean, expressive, and zero boilerplate
public record User(String username, String email) {}
```

---

## 3. What Does Java Generate Under the Hood?

When you declare a `record`, the Java compiler automatically generates:

1. **`final` class:** Inherits directly from `java.lang.Record`. It cannot be extended.
2. **`private final` fields:** One for each component listed in the record header.
3. **Canonical constructor:** A constructor whose signature matches the record header.
4. **Public accessor methods:** Named after the component itself (e.g., `user.username()` and `user.email()`, **not** `getUsername()`).
5. **Implementations of `equals()` and `hashCode()`:** Based on all components.
6. **Implementation of `toString()`:** Outputs a clean representation, like `User[username=alex, email=alex@example.com]`.

---

## 4. How to Use Records in Code

### Basic Usage

```java
public class RecordDemo {
    public static void main(String[] args) {
        User user1 = new User("alex", "alex@example.com");
        User user2 = new User("alex", "alex@example.com");

        // 1. Accessors (Note: no 'get' prefix)
        System.out.println(user1.username()); // "alex"
        System.out.println(user1.email());    // "alex@example.com"

        // 2. Built-in toString()
        System.out.println(user1); // Output: User[username=alex, email=alex@example.com]

        // 3. Built-in value-based equals() & hashCode()
        System.out.println(user1.equals(user2)); // true
        System.out.println(user1.hashCode() == user2.hashCode()); // true
    }
}
```

---

## 5. Record Constructors

Records support three types of constructors:

### 1. Canonical Constructor
The implicit constructor that takes all components and assigns them to the fields. You can also explicitly declare it if you want to customize it:

```java
public record User(String username, String email) {
    public User(String username, String email) {
        this.username = username.toLowerCase();
        this.email = email;
    }
}
```

### 2. Compact Constructor (Best Practice for Validation)
Unique to records! A compact constructor omits the parameter list entirely. It is designed specifically for **input validation and normalization** before fields are assigned:

```java
public record User(String username, String email) {
    // Compact constructor: no parameter list '(String username, String email)'
    public User {
        // Validation
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("Username cannot be empty");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email format");
        }
        
        // Normalization (values are automatically assigned to fields at the end)
        username = username.strip().toLowerCase();
        email = email.strip().toLowerCase();
    }
}
```

> **Why Compact Constructors Are Great:** You don't have to write `this.username = username;`. Java assigns the fields implicitly after the compact constructor body runs.

### 3. Custom / Overloaded Constructors
You can add alternative constructors, but they **must** delegate to the canonical constructor using `this(...)`:

```java
public record User(String username, String email) {
    // Overloaded constructor providing a default email domain
    public User(String username) {
        this(username, username + "@default.com");
    }
}
```

---

## 6. What Records CAN and CANNOT Do

### ✅ What Records CAN Do:
* **Implement Interfaces:** Records can implement any interface (e.g., `Serializable`, `Comparable`, or custom interfaces).
  ```java
  public record Point(int x, int y) implements Comparable<Point> {
      @Override
      public int compareTo(Point other) {
          return Integer.compare(this.x, other.x);
      }
  }
  ```
* **Have Static Fields & Static Methods:**
  ```java
  public record Order(String orderId, double amount) {
      public static final double TAX_RATE = 0.05;

      public static Order zero(String orderId) {
          return new Order(orderId, 0.0);
      }
  }
  ```
* **Have Custom Instance Methods:**
  ```java
  public record Rectangle(double width, double height) {
      public double area() {
          return width * height;
      }
  }
  ```
* **Local Records:** You can declare a record inside a method or loop as an intermediate data holder (Java 16+).
* **Generics:** Records fully support type parameters:
  ```java
  public record ApiResponse<T>(boolean success, T data, String errorMessage) {}
  ```

### ❌ What Records CANNOT Do:
* **Cannot extend other class:** Records cannot extend other classes because they already implicitly extend `java.lang.Record`, and they are implicitly `final` (cannot be inherited by other classes). They can, however, implement interfaces.
* **Cannot declare additional instance fields in the body:** All instance data must be declared directly in the record header.

```java
  public record BadRecord(int id) {
      // ❌ Compile Error: Cannot declare instance field inside record body
      // private String extraData; 
  }
```
* **Cannot have setters:** Fields are implicitly `final`.

---

## 7. Advanced Capabilities with Modern Java

### 1. Pattern Matching with Record Deconstruction (Java 21)
Starting in Java 21, you can deconstruct records directly in `switch` expressions and `instanceof` checks:

```java
public sealed interface Shape permits Circle, Rectangle {}
public record Circle(double radius) implements Shape {}
public record Rectangle(double width, double height) implements Shape {}

public class ShapeCalculator {
    public static double calculateArea(Shape shape) {
        return switch (shape) {
            case Circle(double r) -> Math.PI * r * r;
            case Rectangle(double w, double h) -> w * h;
        };
    }
}
```

### 2. Safer Serialization
Records use a specialized serialization mechanism. Unlike regular Java classes where deserialization can bypass constructors via reflection:
* Records are deserialized **only through their canonical constructor**.
* This guarantees that your validation logic inside the compact constructor is **always executed**, preventing malicious or corrupted payload injection.

---

## 8. POJO vs. Lombok vs. Java Record

| Feature | Traditional POJO | Lombok (`@Value` / `@Data`) | Java Record (Java 16+) |
| :--- | :--- | :--- | :--- |
| **Language Support** | Standard Java | Third-party Library | **Built directly into the Java language** |
| **Boilerplate** | Very high | Minimal | **None (Single line)** |
| **Immutability** | Manual (`final` fields) | Via `@Value` | **Enforced by compiler & JVM** |
| **Accessor Naming** | `getName()` | `getName()` | `name()` |
| **Pattern Matching** | No | No | **Yes (Record patterns in Java 21)** |
| **Reflection / Serialization Security** | Vulnerable to constructor bypass | Vulnerable | **Safe: Deserialization invokes canonical constructor** |
| **JPA `@Entity` Compatibility** | ✅ Yes | ✅ Yes | ❌ **No (JPA requires mutable no-arg entity)** |

---

## 9. The 4 Traps for Interviews

### Trap 1: "Are Records Deeply Immutable?"
* **Answer:** **No, Records are only shallowly immutable.**
* **Why:** The reference to an object is `final`, but the object itself might be mutable. If a record holds a mutable `List` or `Date`, callers can still mutate the list contents.
* **Fix:** Use defensive copying in the compact constructor:
  ```java
  public record Student(String name, List<String> subjects) {
      public Student {
          // Defensive copy to guarantee true immutability
          subjects = List.copyOf(subjects);
      }
  }
  ```

---

### Trap 2: "Can we use a Record as a JPA / Hibernate Entity?"
* **Answer:** **No, avoid using Records as `@Entity`.**
* **Why:** JPA specifications require:
  1. A `public` or `protected` no-argument constructor (Records do not have this).
  2. Non-final classes and non-final fields for lazy-loading proxies and dirty-checking.
* **Where to use them instead:** Records are ideal for **JPA Projections, DTOs (Data Transfer Objects), API Request/Response bodies, and CQRS query models**.

---

### Trap 3: "Why are accessor methods named `field()` instead of `getField()`?"
* **Answer:** Because Records model **data components, not JavaBeans**.
* **Why:** The JavaBean standard (`get...`, `set...`, zero-arg constructor) was designed for stateful UI components in the 1990s. Records model mathematical tuples / state vectors where fields are transparent attributes rather than private encapsulated state.

---

### Trap 4: "Can a Record extend another class or be subclassed?"
* **Answer:** **No.**
* **Why:** All records implicitly extend `java.lang.Record`, and Java does not support multiple class inheritance. Furthermore, records are implicitly `final`, meaning no class can inherit from a record. They can, however, implement any number of interfaces.

---

## 10. How to Answer in an Interview (Exact Wordings)

### Q1: "What is a Record in Java?"
> *"A Record is a specialized, immutable class introduced in Java 16 designed to act as a transparent carrier for data. With a single line of code, the compiler automatically generates private final fields, a canonical constructor, accessors, equals, hashCode, and toString."*

### Q2: "What is a compact constructor and why would you use it?"
> *"A compact constructor is a record constructor that omits the parameter list. It is used to perform validation and normalization on component values before they are assigned to the record's final fields, without having to write repetitive assignment statements."*

### Q3: "What is the difference between Lombok's `@Value` and Java Records?"
> *"Lombok is an external annotation processor that modifies the AST at compile time to generate boilerplate. Records are a first-class language and JVM feature with native compiler support, enhanced serialization security, and full integration with modern pattern matching and switch expressions."*

### Q4: "Where should you use Records in enterprise applications?"
> *"Records are best used for immutable data carriers: API request and response DTOs, Kafka message payloads, value objects in Domain-Driven Design, database query projections, and local aggregate tuples inside complex business logic."*

---

## 11. Recall Checklist

1. **Introduced in:** Java 16 (JEP 395).
2. **Core Purpose:** Transparent carriers for immutable data.
3. **Implicitly:** `final` class extending `java.lang.Record`, with `private final` fields.
4. **Accessors:** `record.fieldName()`, **not** `getFieldName()`.
5. **Validation:** Use **Compact Constructors** (`public RecordName { ... }`).
6. **Inheritance:** Cannot extend classes; can implement interfaces.
7. **Gotcha:** **Shallow immutability** (use `List.copyOf()` for collections).
8. **Entity Rule:** Not for JPA `@Entity`; perfect for **DTOs and Projections**.
