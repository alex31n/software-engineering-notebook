# Pattern Matching in Java

In traditional Java, working with different object types required checking the type with `instanceof` and then explicitly casting it on the next line. Similarly, `switch` statements were limited only to primitives, strings, and enums.

**Pattern Matching** is a feature in Java that allows you to check an object's type and use it immediately as a typed variable, without writing manual casts. It removes repetitive boilerplate, makes code cleaner, and prevents runtime `ClassCastException`.

Finalized in **Java 16 (for `instanceof`)** and **Java 21 (for `switch` and Records)**.

---

## 1. Quick Evolution Timeline

| Feature | Preview | Finalized In | What It Introduced |
| :--- | :--- | :--- | :--- |
| **`instanceof` Pattern Matching** | Java 14 / 15 | **Java 16** (JEP 394) | Eliminates explicit casting after `instanceof`. |
| **Pattern Matching for `switch`** | Java 17 - 20 | **Java 21** (JEP 441) | Type patterns, `when` guards, and `null` handling in `switch`. |
| **Record Patterns** | Java 19 / 20 | **Java 21** (JEP 440) | Deconstructs records directly into component variables. |
| **Unnamed Patterns & Variables** | Java 21 | **Java 22** (JEP 456) | Discard unused components with underscore `_`. |

---

## 2. Why Did We Need This?

### The Old Way: Repetitive Casting and Clunky Logic
```java
// Traditional Java: Clunky, redundant, and unsafe
public void process(Object obj) {
    if (obj instanceof String) {
        String s = (String) obj; // Redundant manual cast!
        if (s.length() > 5) {
            System.out.println(s.toUpperCase());
        }
    }
}
```

### The New Way: Clean, Safe, and Expressive
```java
// Modern Java: Test, cast, bind, and condition in one step
public void process(Object obj) {
    if (obj instanceof String s && s.length() > 5) {
        System.out.println(s.toUpperCase()); // 's' is already in scope and typed!
    }
}
```

---

## 3. Pattern Matching for `instanceof` (Java 16)

A pattern consists of a **target predicate** (the type) and a **binding variable** (the extracted value).

```java
if (obj instanceof String s) {
    // 's' is automatically cast to String and accessible here
    System.out.println("Length: " + s.length());
}
```

### Flow Scoping: Where is the Binding Variable in Scope?
Pattern variables use **flow scoping**—the variable is in scope **only in regions where the test is guaranteed to be `true`**.

#### 1. Valid with `&&` (Short-circuit AND)
```java
// VALID: 's' is only evaluated if obj is indeed a String
if (obj instanceof String s && s.length() > 3) {
    System.out.println(s);
}
```

#### 2. Compile Error with `||` (Short-circuit OR)
```java
// ❌ COMPILE ERROR: If obj is NOT a String, right side executes, but 's' is undefined!
if (obj instanceof String s || s.length() > 3) { ... }
```

#### 3. Inverted Guard & Early Return
```java
public void validate(Object obj) {
    if (!(obj instanceof String s)) {
        return; // Exits if not a String
    }
    // VALID: If we reached here, 's' is definitely a String!
    System.out.println(s.toLowerCase());
}
```

---

## 4. Pattern Matching for `switch` (Java 21)

Java 21 elevates `switch` from a simple value-jump table into a powerful, declarative type-dispatch engine.

### 1. Type Patterns in `case`
You can switch on any object type, not just primitives/strings/enums:

```java
static String formatValue(Object obj) {
    return switch (obj) {
        case Integer i -> String.format("int: %d", i);
        case Long l    -> String.format("long: %d", l);
        case Double d  -> String.format("double: %f", d);
        case String s  -> String.format("String: '%s'", s);
        default        -> obj.toString();
    };
}
```

### 2. Guarded Patterns (`when` clause)
To add extra conditional logic to a `case`, use the **`when`** keyword (note: preview versions used `&&`, but Java 21 finalized on `when`):

```java
static String classifyString(Object obj) {
    return switch (obj) {
        case String s when s.isBlank()      -> "Empty string";
        case String s when s.length() > 100 -> "Long text: " + s.substring(0, 10);
        case String s                       -> "Regular text: " + s;
        default                             -> "Not a string";
    };
}
```

### 3. Native `null` Handling
In legacy Java, `switch(obj)` threw a `NullPointerException` before checking any cases. Modern `switch` allows explicit `null` handling:

```java
static void handle(Object obj) {
    switch (obj) {
        case null                  -> System.out.println("It's null!");
        case String s              -> System.out.println("String: " + s);
        default                    -> System.out.println("Something else");
    }
}
```
> **Tip:** You can also combine `null` with `default`: `case null, default -> ...`

---

## 5. Record Patterns & Deconstruction (Java 21)

Record patterns decompose a record instance into its individual constituent fields in a single step.

### 1. Basic Record Pattern
Given a record:
```java
public record Point(int x, int y) {}
```

Deconstruct directly in `instanceof`:
```java
// Extracts x and y directly into local variables
if (obj instanceof Point(int x, int y)) {
    System.out.println("Coordinates: " + x + ", " + y);
}
```

Or with type inference using `var`:
```java
if (obj instanceof Point(var x, var y)) { ... }
```

### 2. Nested Record Patterns
Pattern matching can recursively unpack nested records:

```java
public record Point(int x, int y) {}
public record Rectangle(Point topLeft, Point bottomRight) {}

static void printDimensions(Object obj) {
    if (obj instanceof Rectangle(Point(int x1, int y1), Point(int x2, int y2))) {
        int width = Math.abs(x2 - x1);
        int height = Math.abs(y2 - y1);
        System.out.println("Dimensions: " + width + " x " + height);
    }
}
```

### 3. Unnamed Patterns with `_` (Java 22)
If you don't need all fields of a record, use the underscore `_` to ignore unused components:

```java
// Only interested in the x-coordinate:
if (obj instanceof Point(int x, _)) {
    System.out.println("X coordinate is: " + x);
}
```

---

## 6. The Holy Trinity: Sealed Classes + Records + Pattern Matching

When combined, these three modern Java features provide **Algebraic Data Types (ADTs)** and **compile-time exhaustive checking**:

```java
// Sealed Hierarchy
public sealed interface OrderStatus permits Placed, Shipped, Delivered, Cancelled {}

public record Placed(long timestamp) implements OrderStatus {}
public record Shipped(String trackingNumber, String carrier) implements OrderStatus {}
public record Delivered(Instant deliveryTime) implements OrderStatus {}
public record Cancelled(String reason) implements OrderStatus {}

public class OrderService {
    public static String getStatusMessage(OrderStatus status) {
        // Compiler guarantees EXHAUSTIVENESS: No 'default' branch needed!
        return switch (status) {
            case Placed(long t)               -> "Order placed at timestamp " + t;
            case Shipped(String track, var c) -> "Shipped via " + c + " (Tracking: " + track + ")";
            case Delivered(var time)          -> "Delivered on " + time;
            case Cancelled(String reason)     -> "Cancelled due to: " + reason;
        };
    }
}
```

> **Why this matters for interviews:**
> If another developer later adds a `Refunded` class to `OrderStatus`, the compiler will **refuse to build** this switch statement until `Refunded` is handled. No hidden runtime bugs!

---

## 7. The 5 Traps for Interviews

### Trap 1: "Case Dominance" (Order of Cases Matters)
* **Rule:** A more general (broader) type pattern **cannot precede** a more specific subtype pattern.
* **Why:** The compiler detects dead code and rejects it.
```java
switch (obj) {
    case CharSequence cs -> ... // ❌ COMPILE ERROR: Dominates the next case!
    case String s       -> ... // Dead code! String is a subtype of CharSequence
}

// ✅ Correct Order: Specific first, general last
switch (obj) {
    case String s        -> ...
    case CharSequence cs -> ...
}
```
* **Guard Dominance:** An unguarded case dominates a guarded case of the same type:
```java
case String s -> ...                  // ❌ Dominates the next case!
case String s when s.length() > 5 -> ...

// ✅ Correct Order:
case String s when s.length() > 5 -> ...
case String s                    -> ...
```

---

### Trap 2: Flow Scoping with `||` vs `&&`
* **Interview Question:** *"Why does `if (obj instanceof String s && s.length() > 0)` compile, but `if (obj instanceof String s || s.length() > 0)` fail?"*
* **Answer:** Flow scoping. In an `&&` expression, the right side only executes if the left side evaluates to `true` (so `s` is guaranteed to exist). In an `||` expression, the right side executes when the left side is `false` (meaning `obj` was **not** a String, so `s` cannot exist).

---

### Trap 3: `switch` with `null` Pointer Behavior
* **Legacy Switch:** Throws `NullPointerException` immediately if the target is `null`.
* **Pattern Switch:**
  * If a `case null` is present, execution flows to that branch cleanly.
  * If no `case null` is present, it **still throws `NullPointerException`** (preserving backwards compatibility).

---

### Trap 4: Exhaustiveness Requirement in `switch`
* **Rule:** A `switch` **expression** (or any pattern `switch`) MUST be exhaustive.
* **How to satisfy it:**
  1. Add a `default` branch, **OR**
  2. Switch over a `sealed` type (or enum) where every permitted subclass is explicitly covered.

---

### Trap 5: Mutation of Pattern Variables
* **Rule:** Pattern binding variables are **effectively final**.
* You cannot reassign them (e.g., `s = "another string";` is disallowed or strongly discouraged depending on context, and compiler treats them as effectively final for lambda captures).

---

## 8. How to Answer in an Interview (Exact Wordings)

### Q1: "What is Pattern Matching in Java?"
> *"Pattern Matching in Java allows you to check an object's type and use it immediately as a typed variable without writing manual casts. First introduced for `instanceof` in Java 16 and expanded to `switch` and `Records` in Java 21, it eliminates repetitive casting boilerplate, makes code cleaner, and avoids runtime `ClassCastException`."*

### Q2: "What is flow scoping in pattern matching?"
> *"Flow scoping means a pattern variable is only in scope where the compiler can mathematically prove the pattern matched. For example, in an `if` statement with `&&`, the variable is in scope on the right-hand side; with `||`, it is not. If an inverted check ends with a `return` or exception, the variable remains in scope for the rest of the method block."*

### Q3: "What are Guarded Patterns and how do they work in Java 21?"
> *"Guarded patterns allow us to add boolean refinement conditions to a `case` pattern using the `when` keyword. The pattern matches only if the type check succeeds and the boolean expression after `when` evaluates to true."*

### Q4: "How do Sealed Classes, Records, and Pattern Matching complement each other?"
> *"Together, they provide Algebraic Data Types in Java. Sealed classes define a closed set of variants (Sum types), Records define immutable data carriers (Product types), and Pattern Matching provides compiler-verified, exhaustive operations over them without requiring defensive `default` branches."*

---

## 9. 60-Second Recall Checklist

1. **Versions:** `instanceof` (Java 16) | `switch` & Records (Java 21) | Unnamed `_` (Java 22).
2. **`instanceof`:** `if (obj instanceof String s && s.length() > 0)` (no manual cast).
3. **Flow Scope:** Valid with `&&`, **invalid** with `||`. Valid after `if (!(o instanceof String s)) return;`.
4. **Switch Guards:** Use `case Type var when condition -> ...`.
5. **Switch Nulls:** Supported directly via `case null -> ...`.
6. **Case Dominance:** Subtypes and guarded cases must be placed **before** parent types and unguarded cases.
7. **Record Deconstruction:** `case Point(int x, int y) -> ...` or nested `case Box(Point(var x, var y))`.
8. **Exhaustiveness:** If switching over a `sealed` class, covering all subclasses removes the need for `default`.
