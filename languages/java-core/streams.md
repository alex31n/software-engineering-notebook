# Java Streams API

In traditional Java, processing collections required verbose, imperative loops (`for`, `while`) mixed with temporary variables, manual filtering, and mutable accumulator lists. This made business logic hard to read, prone to off-by-one bugs, and difficult to parallelize safely.

**Java Streams** (introduced in Java 8) allow you to process sequences of elements **declaratively**. Instead of describing *how* to iterate step-by-step, you describe *what* transformations you want to perform in a clean, readable pipeline.

> **Key Rule of Thumb:**  
> * **Collection** is about **storing** data in memory.  
> * **Stream** is about **computing and processing** data on demand.

---

## 1. Why Did We Need This?

### The Old Way: Imperative & Boilerplate-heavy
```java
// Goal: Find distinct names of active users older than 18, sorted alphabetically
List<String> result = new ArrayList<>();
for (User user : users) {
    if (user.isActive() && user.getAge() > 18) {
        String name = user.getName();
        if (!result.contains(name)) {
            result.add(name);
        }
    }
}
Collections.sort(result);
```

### The New Way: Declarative & Expressive
```java
List<String> result = users.stream()
    .filter(u -> u.isActive() && u.getAge() > 18)
    .map(User::getName)
    .distinct()
    .sorted()
    .toList(); // Clean, readable, and self-documenting
```

---

## 2. Anatomy of a Stream Pipeline

Every stream pipeline always consists of **3 parts**:

```
[ Data Source ]  -->  [ Intermediate Operations ]  -->  [ Terminal Operation ]
(List, Set, Array)     (filter, map, sorted...)          (toList, count, collect...)
                         (Lazy Transformations)             (Triggers Execution)
```

1. **Source:** Where data comes from (e.g., `list.stream()`, `Arrays.stream(arr)`, `Stream.of("a", "b")`).
2. **Intermediate Operations:** Transform the stream into another stream (e.g., `filter`, `map`). They are **lazy** and do not run until a terminal operation is called.
3. **Terminal Operation:** Produces a final result (e.g., `List`, `int`, `boolean`) or a side-effect (`forEach`). It **consumes and closes** the stream.

---

## 3. Four Core Characteristics of Streams

1. **Not a Data Structure:** A stream does not store elements; it carries values from a source through a pipeline of computational steps.
2. **Non-Modifying (Pure):** A stream does not modify the underlying data source. Filtering a list returns a new stream; the original list remains untouched.
3. **Lazy Evaluation:** Intermediate operations are not executed immediately. They are only computed when the terminal operation explicitly demands a result.
4. **Consumable Only Once:** A stream cannot be reused once a terminal operation has executed. Calling another operation on a closed stream throws `IllegalStateException`.

---

## 4. Common Operations Cheatsheet

### 1. Intermediate Operations (Returns `Stream<T>`)

| Operation | Purpose | Example |
| :--- | :--- | :--- |
| **`filter(Predicate)`** | Keeps elements that match a boolean condition | `.filter(n -> n % 2 == 0)` |
| **`map(Function)`** | Transforms each element into another value (1-to-1) | `.map(String::toUpperCase)` |
| **`flatMap(Function)`** | Transforms each element into a stream and flattens it (1-to-many) | `.flatMap(order -> order.getItems().stream())` |
| **`distinct()`** | Removes duplicates (uses `equals()` / `hashCode()`) | `.distinct()` |
| **`sorted()`** | Sorts elements naturally or via a `Comparator` | `.sorted(Comparator.reverseOrder())` |
| **`limit(n)`** | Truncates stream to at most `n` elements | `.limit(5)` |
| **`skip(n)`** | Discards the first `n` elements | `.skip(2)` |
| **`peek(Consumer)`** | Inspects elements without modifying them (for debugging) | `.peek(System.out::println)` |

### 2. Terminal Operations (Produces Result / Closes Stream)

| Operation | Return Type | Purpose |
| :--- | :--- | :--- |
| **`toList()`** (Java 16+) | `List<T>` | Collects into an unmodifiable list |
| **`collect(Collector)`** | Any Collection/Value | Collects into Lists, Sets, Maps, or joined Strings |
| **`forEach(Consumer)`** | `void` | Executes an action for each element |
| **`count()`** | `long` | Returns the total count of elements |
| **`reduce(...)`** | `Optional<T>` / `T` | Combines elements into a single summary value |
| **`findFirst()` / `findAny()`** | `Optional<T>` | Returns the first (or any) element found |
| **`anyMatch(Predicate)`** | `boolean` | `true` if at least one element matches |
| **`allMatch(Predicate)`** | `boolean` | `true` if every element matches |
| **`noneMatch(Predicate)`** | `boolean` | `true` if no elements match |

---

## 5. `map()` vs. `flatMap()` (The #1 Interview Comparison)

| Feature | `map()` | `flatMap()` |
| :--- | :--- | :--- |
| **Mapping Ratio** | **One-to-One** (1 element in $\rightarrow$ 1 element out) | **One-to-Many** (1 element in $\rightarrow$ a stream of elements out) |
| **Return Type** | Returns `Stream<R>` | Returns `Stream<R>` by **flattening** `Stream<Stream<R>>` |
| **When to use** | Changing type or extracting a field | Unpacking nested lists, arrays, or optional values |

### Visual Code Example:
```java
List<List<String>> nested = List.of(
    List.of("A", "B"),
    List.of("C", "D")
);

// map() leaves the inner lists intact -> Stream<List<String>>
List<List<String>> mapResult = nested.stream()
    .map(list -> list)
    .toList(); // Output: [["A", "B"], ["C", "D"]]

// flatMap() flattens inner lists into a single continuous stream -> Stream<String>
List<String> flatMapResult = nested.stream()
    .flatMap(List::stream)
    .toList(); // Output: ["A", "B", "C", "D"]
```

---

## 6. Common `Collectors` Recipes

```java
// 1. Grouping elements by a property (e.g., Department -> List of Employees)
Map<Department, List<Employee>> byDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment));

// 2. Partitioning into two groups (true / false)
Map<Boolean, List<Student>> passingFailing = students.stream()
    .collect(Collectors.partitioningBy(s -> s.getGrade() >= 60));

// 3. Joining strings with a delimiter
String joined = List.of("Java", "Kotlin", "Scala").stream()
    .collect(Collectors.joining(", ", "[", "]")); // Output: "[Java, Kotlin, Scala]"

// 4. Summarizing / Calculating statistics
DoubleSummaryStatistics stats = numbers.stream()
    .collect(Collectors.summarizingDouble(Double::doubleValue));
// stats.getAverage(), stats.getMax(), stats.getSum()
```

---

## 7. Parallel Streams: When to Use and When to Avoid

Calling `.parallelStream()` splits workload across multiple threads using the shared **ForkJoinPool.commonPool()**.

### ✅ When to Use:
* Large datasets (thousands/millions of elements).
* Computationally intensive, CPU-bound tasks.
* Operations that are **stateless, non-interfering, and associative** (order doesn't matter).

### ❌ When to Avoid:
* **I/O-bound tasks** (e.g., HTTP calls, database queries): Blocking a parallel stream blocks threads in the shared common pool, starving the entire JVM.
* **Shared mutable state**: Causes race conditions without complex synchronization.
* Small collections: The thread overhead exceeds the processing speedup.

---

## 8. The 5 Traps for Interviews

### Trap 1: Reusing a Stream
* **Problem:** Streams cannot be consumed more than once.
```java
Stream<String> stream = List.of("a", "b", "c").stream();
stream.forEach(System.out::println); // ✅ OK (Terminal op executed)
stream.count(); // ❌ Runtime Exception: IllegalStateException: stream has already been operated upon or closed
```
* **Fix:** Re-create the stream from the source collection.

---

### Trap 2: Modifying the Source During Streaming (ConcurrentModificationException)
* **Problem:** Streams expect the underlying source to remain unmodified during pipeline execution.
```java
List<String> list = new ArrayList<>(List.of("a", "b", "c"));
list.stream().forEach(item -> list.add("d")); // ❌ Throws ConcurrentModificationException
```

---

### Trap 3: Expecting `peek()` to Always Run
* **Problem:** Developers often misuse `peek()` to modify state or assume it runs for every element.
```java
// Optimization: count() might skip processing elements if size is known!
long count = Stream.of("one", "two", "three")
    .peek(System.out::println) // Might never print!
    .count();
```
* **Rule:** `peek()` is strictly for debugging/logging, never for executing business logic.

---

### Trap 4: `Stream.toList()` vs `Collectors.toList()`
* **`Stream.toList()` (Java 16+):** Returns an **unmodifiable list**. It is faster, uses less memory, and rejects modifications (`list.add(...)` throws `UnsupportedOperationException`).
* **`Collectors.toList()`:** Returns a standard mutable `ArrayList` (no immutability guarantee).

---

### Trap 5: Forgetting that Intermediate Operations are Lazy
* **Problem:** Without a terminal operation, nothing happens.
```java
// Nothing is printed! filter() and map() never execute because there is no terminal operation.
names.stream()
    .filter(n -> {
        System.out.println("Filtering: " + n);
        return n.startsWith("A");
    });
```

---

## 9. How to Answer in an Interview (Exact Wordings)

### Q1: "What is the Java Stream API?"
> *"The Java Stream API, introduced in Java 8, is a functional programming pipeline used to process sequences of elements declaratively. Unlike collections, streams do not store data or modify the source; they describe transformations like filtering, mapping, and aggregating, which are executed lazily when a terminal operation is invoked."*

### Q2: "What is the difference between intermediate and terminal operations?"
> *"Intermediate operations transform a stream into another stream and are lazy—meaning they don't do any work until a terminal operation is called. Terminal operations consume the stream, trigger the actual pipeline computation, and produce a final result like a List, number, or side effect, closing the stream permanently."*

### Q3: "What is the difference between `map` and `flatMap`?"
> *"Both are intermediate operations, but `map` performs a 1-to-1 transformation, converting each element into another object. `flatMap` performs a 1-to-many transformation, where each element produces a new stream, and all resulting streams are flattened into a single continuous stream."*

### Q4: "Why are streams evaluated lazily?"
> *"Lazy evaluation improves performance through optimizations like loop fusion and short-circuiting. Instead of making multiple passes over a collection for each intermediate step, Java combines them into a single pass and stops processing as soon as a terminal operation like `findFirst()` or `anyMatch()` gets its answer."*

---

## 10. 60-Second Recall Checklist

1. **Definition:** Declarative pipeline for processing data; does not store data or mutate source.
2. **3 Parts:** Source $\rightarrow$ Intermediate Operations (Lazy) $\rightarrow$ Terminal Operation (Eager, closes stream).
3. **Consumable Once:** Calling another operation on a closed stream throws `IllegalStateException`.
4. **`map` vs `flatMap`:** `map` is 1-to-1 (`Stream<T> -> Stream<R>`); `flatMap` flattens nested streams (`Stream<List<T>> -> Stream<T>`).
5. **Modern Collection:** Use `.toList()` (Java 16+) for unmodifiable lists instead of `.collect(Collectors.toList())`.
6. **Short-Circuiting:** `limit()`, `findFirst()`, `anyMatch()` stop processing early as soon as the condition is met.
7. **Parallel Rule:** Good for large, CPU-bound, stateless operations; avoid for I/O-bound tasks.
