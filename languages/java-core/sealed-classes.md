# Sealed Class and Interface

In object-oriented programming, inheritance is traditionally an "all-or-nothing" choice:
* A class is either **`final`** (no class can extend it), or
* A class is **`public`** (any class in any package can extend it).

There was no middle ground for an API designer to say: *"I want to allow inheritance, but strictly control WHICH classes are allowed to extend mine."*

**Sealed Class and Interface** in Java are feature introduced to restrict and control class inheritance. Introduced in preview in Java 15 and finalized in Java 17, they allow a developer to declare explicitly which other classes or interfaces are permitted to extend or implement them.

---

## 1. Why Did We Need This?

Before Java 17, modeling a closed domain—such as a fixed set of financial transaction types or shapes—was difficult:

```java
// Anyone in any library could extend this class without your knowledge
public abstract class PaymentMethod {
    // ...
}

public class CreditCard extends PaymentMethod {}
public class PayPal extends PaymentMethod {}
// ⚠️ What prevents a third-party developer from doing this?
public class UnsupportedCryptoCoin extends PaymentMethod {}
```

### The Problems:
1. **Unbounded Inheritance:** You could not prevent foreign code or downstream consumers from subclassing your domain models.
2. **Defensive Coding:** In `switch` or `if-else` chains, you always had to write a `default` branch throwing runtime exceptions, even if you knew all valid types.
3. **No Compiler Warning on New Types:** Adding a new subclass required hunting through the codebase manually to find all places where instances were handled.

### The Solution:
Sealed classes allow you to explicitly define a whitelist of permitted subclasses using the `sealed` and `permits` keywords:

```java
public abstract sealed class PaymentMethod 
    permits CreditCard, PayPal, BankTransfer {
    // Only these 3 classes are allowed to extend PaymentMethod
}
```

---

## 2. Core Syntax and the Three Subclass Modifiers

Every permitted subclass of a sealed class **MUST** explicitly choose one of three modifiers to declare its own inheritance policy:

1. **`final`**: Prevents any further extension.
2. **`sealed`**: Extends the parent and continues the restricted inheritance pattern with its own `permits` clause.
3. **`non-sealed`**: Explicitly re-opens the class hierarchy, allowing any class to extend it.

```java
// 1. The Sealed Base Class
public abstract sealed class Vehicle 
    permits Car, Truck, Bicycle {}

// 2. Subclass 1: Closes inheritance permanently
public final class Car extends Vehicle {}

// 3. Subclass 2: Continues controlled inheritance
public sealed class Truck extends Vehicle 
    permits HeavyTruck, LightTruck {}

public final class HeavyTruck extends Truck {}
public final class LightTruck extends Truck {}

// 4. Subclass 3: Re-opens inheritance to any class
public non-sealed class Bicycle extends Vehicle {}

// Allowed because Bicycle is non-sealed:
public class MountainBike extends Bicycle {}
```

> **Note on `non-sealed`:** This is Java's first hyphenated keyword. It was introduced with a hyphen so existing code using identifiers like `nonSealed` would not break backward compatibility.

---

## 3. The 3 Golden Rules of Sealed Hierarchies

For code to compile, sealed types and their subclasses must adhere to three strict constraints:

### Rule 1: Same Module or Same Package
* If using the Java Module System (`module-info.java`), the sealed class and its permitted subclasses must reside in the **same named module**.
* If running on the classpath (unnamed module), they must reside in the **exact same package**.

### Rule 2: Direct Inheritance
Every class listed in the `permits` clause must **directly extend** the sealed class or **directly implement** the sealed interface.

### Rule 3: Explicit Subclass Status
Every permitted class must be explicitly marked as `final`, `sealed`, or `non-sealed`. (Records are an exception because records are implicitly `final`!).

---

## 4. Omitting the `permits` Clause

If all permitted subclasses are declared in the **same source file** as the sealed class, the compiler can automatically infer the permitted list. You can omit the `permits` keyword entirely:

```java
// 'permits Circle, Square' is inferred automatically by the compiler
public sealed class Shape {}

final class Circle extends Shape {}
final class Square extends Shape {}
```

---

## 5. Sealed Interfaces

Interfaces can also be sealed. Permitted types can be either implementing classes or extending sub-interfaces:

```java
public sealed interface ServiceResponse 
    permits SuccessResponse, ErrorResponse, AsyncResponse {}

// Classes implementing the sealed interface
public final class SuccessResponse implements ServiceResponse {}
public final class ErrorResponse implements ServiceResponse {}

// Sub-interfaces must be either 'sealed' or 'non-sealed' (interfaces cannot be final)
public sealed interface AsyncResponse extends ServiceResponse 
    permits PollingResponse, StreamingResponse {}

public final class PollingResponse implements AsyncResponse {}
public final class StreamingResponse implements AsyncResponse {}
```

> **Important Rule for Interfaces:** An interface can **never** be declared `final`. Therefore, a sub-interface extending a sealed interface must be declared as either **`sealed`** or **`non-sealed`**.

---

## 6. Sealed Classes + Records + Pattern Matching (The Power Trio)

When combined with **Java Records** and **Pattern Matching for Switch (Java 21)**, sealed classes unlock full **Algebraic Data Types (Sum Types)** in Java.

Because **Records are implicitly `final`**, they seamlessly serve as permitted types without any extra modifier:

```java
// 1. Sealed Interface with Record Implementations
public sealed interface OrderEvent 
    permits OrderPlaced, OrderShipped, OrderCancelled {}

public record OrderPlaced(String orderId, double amount) implements OrderEvent {}
public record OrderShipped(String orderId, String trackingNumber) implements OrderEvent {}
public record OrderCancelled(String orderId, String reason) implements OrderEvent {}
```

### Exhaustive Pattern Matching Without `default`:
Because the compiler knows **all** possible implementations of `OrderEvent`, the `switch` statement is guaranteed to be **exhaustive**. You do not need a `default` case!

```java
public class OrderProcessor {
    public static String handleEvent(OrderEvent event) {
        // Compiler verifies that EVERY permitted type is handled!
        return switch (event) {
            case OrderPlaced(String id, double amt) -> 
                "Order " + id + " placed with amount $" + amt;
            case OrderShipped(String id, String tracking) -> 
                "Order " + id + " shipped with tracking " + tracking;
            case OrderCancelled(String id, String reason) -> 
                "Order " + id + " cancelled due to: " + reason;
        };
    }
}
```

> **The Major Advantage:** If someone adds a new permitted record tomorrow (e.g., `OrderRefunded`), the code will **fail at compile time** on the switch statement above, pointing you directly to every place in your codebase that needs updating!

---

## 7. Subclass Modifiers Comparison

| Modifier | What It Means | Can It Be Subclassed? |
| :--- | :--- | :--- |
| **`final`** | Closes the hierarchy completely. | ❌ No |
| **`sealed`** | Continues controlled inheritance with its own `permits` clause. | ⚠️ Yes, but only by permitted types |
| **`non-sealed`** | Re-opens inheritance to the entire world. | ✅ Yes, by any class |

---

## 8. The 4 Traps for Interviews

### Trap 1: "Can an interface be marked `final`?"
* **Answer:** **No.**
* **Why:** In Java, interfaces cannot be `final` because an interface that cannot be implemented is useless. When a sub-interface extends a sealed interface, it must be marked either **`sealed`** or **`non-sealed`**.

---

### Trap 2: "Can permitted subclasses live in a different package?"
* **Answer:** **No, unless they are in the same named module.**
* **Why:** If the classes are in the unnamed module (standard classpath), they **must** belong to the same package. If they are in a named module (`module-info.java`), they can be in different packages as long as both reside inside the **same module**. They can **never** span across different modules.

---

### Trap 3: "Why don't records need the `final` keyword when implementing a sealed interface?"
* **Answer:** Because **Records are implicitly `final`**.
* **Why:** Java records cannot be extended by design. Therefore, declaring `public final record Point(...)` is redundant—the compiler satisfies the subclass requirement automatically.

---

### Trap 4: "How do Sealed Classes differ from `enum`?"
* **Answer:** `enum` restricts the number of **instances** (values), while Sealed Classes restrict the number of **subtypes** (classes).
* **Why:**
  * An `enum` has fixed, singleton instances known at compile time (e.g., `Status.PENDING`, `Status.ACTIVE`).
  * A sealed hierarchy allows multiple instances of each permitted type, where each instance can maintain its own unique state (e.g., `Circle(5.0)` and `Circle(12.5)`).

---

## 9. How to Answer in an Interview (Exact Wordings)

### Q1: "What are Sealed Classes in Java?"
> *"Sealed classes, introduced in Java 17, allow developers to restrict which other classes or interfaces can extend or implement them using the `sealed` and `permits` keywords. They provide a controlled middle ground between completely open public inheritance and completely closed final classes."*

### Q2: "What modifiers must a subclass of a sealed class have?"
> *"Every permitted subclass must explicitly declare one of three modifiers: `final` to stop inheritance, `sealed` to continue controlled inheritance, or `non-sealed` to re-open inheritance to any class. Records do not need a modifier because they are implicitly final."*

### Q3: "How do Sealed Classes improve Pattern Matching in Java 21?"
> *"Because the compiler knows all permitted subtypes of a sealed hierarchy, it can perform exhaustiveness checks in `switch` expressions. This eliminates the need for redundant `default` branches and produces compile-time errors if a new subtype is added without being handled."*

---

## 10. Recall Checklist

1. **Introduced in:** Java 17 (JEP 409).
2. **Keywords:** `sealed`, `permits`, `non-sealed`.
3. **Subclass Requirements:** Must be `final`, `sealed`, or `non-sealed`.
4. **Boundary:** Permitted subclasses must belong to the **same package** or **same named module**.
5. **Records Integration:** Records are implicitly `final`, making them ideal permitted implementations.
6. **Pattern Matching Benefit:** Enables **exhaustive `switch` expressions** without needing a `default` case.
