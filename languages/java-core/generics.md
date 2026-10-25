# Java Generics

Before Java 5, collections stored everything as plain `Object` references. Developers had to manually cast objects when reading them from a collection, and a single wrong cast would silently pass compilation only to crash at runtime with a `ClassCastException`.

**Generics** allow you to parameterize types (classes, interfaces, and methods). They provide **compile-time type safety** by allowing the compiler to enforce type constraints and automatically handle casting for you.

---

## 1. Why Did We Need This?

### The Old Way: Raw Types & Runtime Crashes (Pre-Java 5)
```java
// Raw collection: accepts ANY object
List list = new ArrayList();
list.add("Hello");
list.add(100); // ⚠️ Accidental integer added

// Manual casting required, crashes at runtime!
for (Object obj : list) {
    String s = (String) obj; // 💥 ClassCastException when reaching 100!
}
```

### The New Way: Type-Safe with Generics (Java 5+)
```java
// Parameterized collection: only accepts Strings
List<String> list = new ArrayList<>();
list.add("Hello");
// list.add(100); // ❌ COMPILE ERROR: caught immediately in IDE!

for (String s : list) {
    System.out.println(s); // ✅ No manual casting needed!
}
```

---

## 2. Standard Type Parameter Naming Conventions

By convention, type parameters are single uppercase letters:
* **`T`** - Type (general purpose)
* **`E`** - Element (used extensively by Java Collections Framework)
* **`K`** - Key (used in Maps)
* **`V`** - Value (used in Maps)
* **`N`** - Number

---

## 3. Generic Classes, Interfaces, and Methods

### 1. Generic Class
```java
public class Box<T> {
    private T content;

    public void set(T content) { this.content = content; }
    public T get() { return content; }
}

// Usage:
Box<String> stringBox = new Box<>();
stringBox.set("Java");
String item = stringBox.get(); // No cast needed
```

### 2. Generic Method
A generic method introduces its own type parameter (declared **before the return type**), independent of any class-level generic type:

```java
public class Utility {
    // <T> before the return type declares this as a generic method
    public static <T> void swap(T[] array, int i, int j) {
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

---

## 4. Bounded Type Parameters

You can restrict the types that can be passed to a type parameter using the **`extends`** keyword:

### 1. Upper Bound (`<T extends Number>`)
Restricts `T` to `Number` or any of its subclasses (`Integer`, `Double`, etc.):

```java
public class Calculator<T extends Number> {
    public double doubleValue(T number) {
        return number.doubleValue(); // Safe to invoke Number methods!
    }
}
```

### 2. Multiple Bounds (`<T extends Class & Interface1 & Interface2>`)
If a type parameter has multiple bounds, the **class must be listed first**, followed by interfaces:

```java
// Valid: Class first, then interface
public <T extends Number & Comparable<T>> void process(T item) { ... }

// ❌ Invalid: Interface cannot come before class
// public <T extends Comparable<T> & Number> void process(T item) { ... }
```

---

## 5. Wildcards (`?`) and the PECS Principle

A wildcard (`?`) represents an unknown type.

### The Three Types of Wildcards:
1. **Unbounded Wildcard (`<?>`):** Any type (`List<?>`). You can only read elements as `Object`. You cannot add anything except `null`.
2. **Upper Bounded Wildcard (`<? extends T>`):** Represents `T` or any subtype of `T`.
3. **Lower Bounded Wildcard (`<? super T>`):** Represents `T` or any supertype of `T`.

---

### The PECS Rule: **Producer Extends, Consumer Super**

This is the most critical Generics principle for design and interviews:

| Role | Keyword | When to Use | Can You Read? | Can You Write? |
| :--- | :--- | :--- | :--- | :--- |
| **Producer** | `? extends T` | You only **read / get** data from the collection | ✅ Yes (as type `T`) | ❌ **No** (compiler doesn't know exact subtype) |
| **Consumer** | `? super T` | You only **write / add** data into the collection | ⚠️ Only as `Object` | ✅ **Yes** (safe to add `T` or subtypes of `T`) |

#### The Canonical Example: `Collections.copy()`
Look at the JDK's own implementation of `Collections.copy`:
```java
// src produces items (reads) -> extends
// dest consumes items (writes) -> super
public static <T> void copy(List<? super T> dest, List<? extends T> src) {
    for (int i = 0; i < src.size(); i++) {
        dest.set(i, src.get(i));
    }
}
```

#### Why Can't You Add to `List<? extends Number>`?
```java
List<? extends Number> list = new ArrayList<Integer>();
// list.add(10); // ❌ COMPILE ERROR!
```
* **Why:** The compiler knows `list` holds *some* subtype of `Number`, but it doesn't know *which one*. It could be a `List<Double>` or `List<BigDecimal>`. Adding an `Integer` to a `List<Double>` would break type safety!

---

## 6. Type Erasure (How Generics Work Under the Hood)

Java Generics were introduced in Java 5 with an explicit design constraint: **100% backward compatibility with pre-Java 5 bytecode.**

To achieve this, the Java compiler uses **Type Erasure**:
1. At compile time, the compiler inspects and enforces all generic types.
2. It then **erases (removes)** all type parameters, replacing them with their upper bound (or `Object` if unbounded).
3. It automatically inserts explicit casts in the bytecode wherever elements are read.

### What the Compiler Writes vs. What the JVM Sees:
```java
// What You Write:
List<String> list = new ArrayList<>();
list.add("hello");
String s = list.get(0);

// What the JVM Executes after Type Erasure:
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0); // Compiler inserted cast
```

---

## 7. Limitations Caused by Type Erasure

Because generic type information does not exist at runtime, Java imposes several strict rules:

| Limitation | Invalid Code | Why? |
| :--- | :--- | :--- |
| **1. No Primitive Types** | `List<int>` ❌ | Primitives do not inherit from `Object` (use `List<Integer>`) |
| **2. Cannot Instantiate Type** | `new T()` ❌ | Type `T` is erased to `Object` at runtime |
| **3. Cannot Create Generic Array** | `new T[10]` ❌ | Arrays need reified runtime type information; generics do not have it |
| **4. Cannot Use `instanceof` with Type** | `obj instanceof List<String>` ❌ | Runtime only knows it is a `List`, not a `List<String>` (use `List<?>`) |
| **5. No Static Generic Fields** | `private static T item;` ❌ | Static members are shared across all instances regardless of `T` |
| **6. Cannot Catch Generic Exception** | `catch (T e)` ❌ | Exception mechanism relies on runtime type identification |

---

## 8. Invariance vs. Covariance: Why `List<String>` is NOT a `List<Object>`

* **Arrays are Covariant:** `String[]` is considered a subtype of `Object[]`. This was a known design flaw in early Java because it permits runtime errors:
```java
String[] strArr = new String[2];
Object[] objArr = strArr; // Allowed!
objArr[0] = 100; // 💥 ArrayStoreException at runtime!
```

* **Generics are Invariant:** `List<String>` is **NOT** a subtype of `List<Object>`.
```java
List<String> strList = new ArrayList<>();
// List<Object> objList = strList; // ❌ COMPILE ERROR!
```
* **Why:** If Java allowed this, you could write `objList.add(100)`, putting an Integer into what is actually a list of Strings, breaking type safety. To allow subtyping safely, Java requires wildcards: `List<? extends Object>`.

---

## 9. The 5 Traps for Interviews

### Trap 1: Overloading Methods with Erased Signatures
* **Problem:** You cannot overload methods whose parameter types erase to the same raw type:
```java
// ❌ COMPILE ERROR: Both methods erase to process(List list)
public void process(List<String> list) { ... }
public void process(List<Integer> list) { ... }
```

---

### Trap 2: `List<?>` vs. `List<Object>`
* `List<Object>` says: *"I accept any object, and you can add any object to me."*
* `List<?>` says: *"I am a list of some specific unknown type. You can read objects from me, but you CANNOT add anything to me (except `null`)."*

---

### Trap 3: Heap Pollution with Varargs
* Combining Generics with Varargs creates an array of generic types behind the scenes, causing potential heap pollution.
* Use the **`@SafeVarargs`** annotation on `static`, `final`, or `private` methods to suppress compiler warnings when you are certain the method does not store invalid types in the array.

---

### Trap 4: Creating Generic Arrays
* You cannot write `T[] array = new T[capacity];`.
* **Fix (Idiomatic):**
```java
// Approach 1: Cast an Object array (internal collection pattern, like ArrayList)
@SuppressWarnings("unchecked")
T[] array = (T[]) new Object[capacity];

// Approach 2: Reflection (type-safe)
T[] array = (T[]) Array.newInstance(clazz, capacity);
```

---

### Trap 5: Multiple Bounds Syntax Rule
* A class can only extend **one class**, but can implement **multiple interfaces**.
* The class must **always come first**: `<T extends Number & Runnable>` is valid; `<T extends Runnable & Number>` will not compile.

---

## 10. How to Answer in an Interview (Exact Wordings)

### Q1: "What are Generics in Java and why were they introduced?"
> *"Generics, introduced in Java 5, allow classes, interfaces, and methods to operate on parameterized types. They were introduced to provide compile-time type safety and eliminate the need for manual type casting, transforming runtime `ClassCastException` bugs into immediate compile-time errors."*

### Q2: "What is Type Erasure and why does Java use it?"
> *"Type Erasure is the compiler process where all generic type parameters are stripped away after type checking and replaced by their bound (or `Object`) in bytecode. Java uses type erasure to maintain 100% backward compatibility with pre-Java 5 legacy code and JVM bytecode."*

### Q3: "What is the PECS rule and when do you use it?"
> *"PECS stands for 'Producer Extends, Consumer Super'. If your method reads from a generic collection (it produces data for your method to use), use `? extends T`. If your method writes to a generic collection (it consumes data supplied by your method), use `? super T`. If you do both, use exact type `T`."*

### Q4: "Why is `List<String>` not a subtype of `List<Object>`?"
> *"Because Generics are invariant. If `List<String>` were a subtype of `List<Object>`, we could assign a string list to an object list reference and insert integers or dates into it, which violates type safety. Java prevents this at compile time."*

---

## 11. 60-Second Recall Checklist

1. **Purpose:** Compile-time type safety; eliminates manual casting and `ClassCastException`.
2. **Naming:** `T` (Type), `E` (Element), `K` (Key), `V` (Value).
3. **PECS:** **P**roducer **E**xtends (read-only), **C**onsumer **S**uper (write-enabled).
4. **Type Erasure:** Generic types are erased to `Object` (or bound) at compile time for backward compatibility.
5. **Invariance:** `List<String>` is NOT a `List<Object>`.
6. **Limitations:** No primitives (`List<int>` ❌), no `new T()`, no `new T[]`, no `instanceof List<String>`.
7. **Multiple Bounds:** Class must come first (`<T extends Number & Comparable<T>>`).
8. **Method Signature Trap:** `void m(List<String> l)` and `void m(List<Integer> l)` cannot coexist due to type erasure.
