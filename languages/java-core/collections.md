# Java Collections Framework

The **Java Collections Framework (JCF)** is a unified architecture for storing, accessing, and manipulating groups of objects. It provides high-performance, reusable data structures (lists, sets, queues, maps) and algorithms (sorting, searching) out of the box.

> **Key Rule of Thumb:**  
> * **`Collection`** is for individual elements (`List`, `Set`, `Queue`).  
> * **`Map`** is for key-value pairs and **does not** inherit from `Collection`.

---

## 1. High-Level Hierarchy

```
                    Iterable<E>
                        │
                  Collection<E>
         ┌──────────────┼──────────────┐
       List<E>        Set<E>        Queue<E>
         │              │              │
     ArrayList       HashSet       ArrayDeque
     LinkedList   LinkedHashSet   PriorityQueue
                    TreeSet        LinkedList
                 (SortedSet)

─────────────────────────────────────────────────
                     Map<K, V>  (Separate Hierarchy!)
         ┌──────────────┼──────────────┐
      HashMap     LinkedHashMap     TreeMap
                                  (SortedMap)
```

---

## 2. The Big 4 Interfaces: When to Use What

| Interface | Duplicates? | Order Preserved? | Key Characteristics | Common Use Cases |
| :--- | :--- | :--- | :--- | :--- |
| **`List`** | ✅ Yes | ✅ Yes (Insertion) | Positional access by zero-based index | Storing ordered items, sequences |
| **`Set`** | ❌ No | Depends on impl. | Fast uniqueness checks; mathematical sets | De-duplication, tracking membership |
| **`Queue`** | ✅ Yes | ✅ FIFO / Priority | Buffer elements for processing | Task scheduling, breadth-first search |
| **`Map`** | Keys: ❌<br>Values: ✅ | Depends on impl. | Fast lookup by key | Caches, lookups, indexing by ID |

---

## 3. Core Implementations Compared

### 1. `List`: `ArrayList` vs. `LinkedList`

| Feature | `ArrayList` (Default Choice) | `LinkedList` |
| :--- | :--- | :--- |
| **Underlying Data Structure** | Resizable dynamic array | Doubly-linked list (nodes with prev/next) |
| **Random Access (`get(i)`)** | **$O(1)$** (Instant) | **$O(n)$** (Must traverse nodes) |
| **Add/Remove at End** | **$O(1)$ amortized** | **$O(1)$** |
| **Add/Remove in Middle** | $O(n)$ (Shifts elements) | $O(n)$ (Must traverse to node, then $O(1)$ pointer flip) |
| **Memory & Cache Locality** | **Low overhead, CPU cache-friendly** | **High overhead** (each node allocates an object + 2 pointers) |

> **Interview Verdict:** Always prefer **`ArrayList`**. Due to modern CPU cache locality and contiguous memory, `ArrayList` consistently outperforms `LinkedList` in virtually all practical scenarios—even for insertions!

---

### 2. `Set`: `HashSet` vs. `LinkedHashSet` vs. `TreeSet`

| Implementation | Ordering Guarantee | Time Complexity | Null Elements Allowed? | Internal Mechanism |
| :--- | :--- | :--- | :--- | :--- |
| **`HashSet`** | None (Unordered) | **$O(1)$** (add / get / remove) | ✅ Yes (1 null) | Backed by a `HashMap` |
| **`LinkedHashSet`** | **Insertion order** | **$O(1)$** | ✅ Yes (1 null) | `HashMap` + Doubly-linked list |
| **`TreeSet`** | **Sorted order** (Natural/Comparator) | **$O(\log n)$** | ❌ **No** | Backed by a Red-Black Tree (`TreeMap`) |

---

### 3. `Map`: `HashMap` vs. `LinkedHashMap` vs. `TreeMap`

| Implementation | Ordering Guarantee | Time Complexity | Null Keys/Values? | Best Used For |
| :--- | :--- | :--- | :--- | :--- |
| **`HashMap`** | None | **$O(1)$** | 1 null key, many null values | General-purpose fast lookups |
| **`LinkedHashMap`**| **Insertion or Access order** | **$O(1)$** | 1 null key, many null values | LRU Caches, maintaining order |
| **`TreeMap`** | **Sorted by key** | **$O(\log n)$** | ❌ No null keys, null values OK | Range queries (`subMap`, `headMap`), sorted keys |

---

## 4. How `HashMap` Works Internally (The #1 Interview Topic)

A `HashMap` stores data in an array of buckets (`Node<K, V>[] table`).

```
Bucket Index:  [ 0 ] ──> [ Node1 ] ──> [ Node2 ] (Linked List)
               [ 1 ] ──> null
               [ 2 ] ──> [ Red-Black Tree Root ] (Treeified if >= 8 nodes)
               [ 3 ] ──> [ Node3 ]
```

### 1. The `put(K, V)` Workflow
1. **Hash Calculation:** The key's `hashCode()` is scrambled using a helper function to reduce collisions:  
   `hash = (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16)`.
2. **Bucket Calculation:** Uses bitwise AND for instant indexing:  
   `index = (n - 1) & hash` (where `n` is table capacity, always a power of 2).
3. **Collision Handling:**
   * If bucket is empty $\rightarrow$ create new node.
   * If bucket has items (Collision) $\rightarrow$ traverse linked list. If `equals()` matches an existing key, overwrite value. Otherwise, append to end.
4. **Treeification (Java 8+):**
   * If a single bucket list grows to **$\ge 8$ nodes** AND the total table capacity is **$\ge 64$**, the linked list converts into a **Red-Black Tree**.
   * Lookup drops from **$O(n)$** down to **$O(\log n)$**, preventing denial-of-service hash-collision attacks.
   * If elements shrink to **$\le 6$**, it untreeifies back to a linked list.

### 2. Capacity, Load Factor, and Resizing
* **Default Initial Capacity:** `16` (always power of 2).
* **Default Load Factor:** `0.75` (optimal trade-off between time and space).
* **Threshold for Resizing:** `Capacity * Load Factor = 16 * 0.75 = 12`.
* **Resizing:** When the number of elements exceeds 12, capacity **doubles** (16 $\rightarrow$ 32), and all elements are rehashed/re-indexed.

---

## 5. The `equals()` and `hashCode()` Contract

If you use custom objects as `HashMap` keys or `HashSet` elements, you **MUST** override both:

1. **If `a.equals(b) == true`**, then **`a.hashCode() == b.hashCode()`** MUST be `true`.
2. **If `a.hashCode() == b.hashCode()`**, `a.equals(b)` is **NOT** required to be true (this is a collision).
3. If you break this rule, two identical objects will produce different hashcodes, land in different buckets, and `.get(key)` will return `null`!

---

## 6. Thread-Safe Collections: `HashMap` vs. `ConcurrentHashMap`

| Feature | `HashMap` | `Hashtable` (Legacy) | `ConcurrentHashMap` |
| :--- | :--- | :--- | :--- |
| **Thread-Safe?** | ❌ No | ✅ Yes | ✅ Yes |
| **Locking Strategy** | None | Locks entire table via `synchronized` methods | **Bucket-level locks** (CAS for empty buckets + synchronized only on bucket head) |
| **Performance** | Fastest (single thread) | Terrible (single bottleneck) | **High concurrency throughput** |
| **Null Keys/Values**| ✅ Allowed | ❌ Throws NPE | ❌ Throws NPE (avoids ambiguity in concurrent reads) |

---

## 7. Fail-Fast vs. Fail-Safe Iterators

| Feature | Fail-Fast Iterators | Fail-Safe (Weakly Consistent) |
| :--- | :--- | :--- |
| **Exception on Mod?** | **Throws `ConcurrentModificationException`** | **Never throws exception** |
| **How it detects change**| Tracks an internal modification counter (`modCount`) | Operates on a clone or snapshot of the data |
| **Examples** | `ArrayList`, `HashSet`, `HashMap` iterators | `ConcurrentHashMap`, `CopyOnWriteArrayList` |

### Safe Removal During Iteration:
```java
// ❌ WRONG: Throws ConcurrentModificationException!
for (String s : list) {
    if (s.equals("removeMe")) list.remove(s);
}

// ✅ RIGHT (Option 1): Use Iterator's own remove()
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("removeMe")) it.remove();
}

// ✅ RIGHT (Option 2 - Java 8+): Use removeIf()
list.removeIf(s -> s.equals("removeMe"));
```

---

## 8. Ways to Create Immutable Lists (Java 9+)

| Method | Can Modify? | Allows `null`? | Backed by Original? |
| :--- | :--- | :--- | :--- |
| **`Arrays.asList(arr)`** | Fixed-size (`set()` works, `add()`/`remove()` fail) | ✅ Yes | ✅ Yes (changing array changes list!) |
| **`Collections.unmodifiableList(list)`** | Read-only view (throws exception on write) | ✅ Yes | ✅ Yes (if underlying list changes, view changes) |
| **`List.of(...)` (Java 9+)** | **Truly Immutable** (all writes throw exception) | ❌ Throws NPE | ❌ Completely independent, space-efficient |

---

## 9. The 5 Traps for Interviews

### Trap 1: Mutating a Key Inside a `HashMap`
* **Problem:** If an object's field used in `hashCode()` is mutated while it is acting as a key:
* **Consequence:** Its hashcode changes, meaning `map.get(key)` will search the wrong bucket and return `null`!
* **Rule:** Keys in a `HashMap` should **always be immutable** (e.g., `String`, `Integer`, or Java `record`).

---

### Trap 2: Using `Arrays.asList()` and Trying to `add()`
```java
List<String> list = Arrays.asList("A", "B");
list.add("C"); // 💥 UnsupportedOperationException!
```
* **Why:** `Arrays.asList` returns a private, fixed-size wrapper around the array, not a `java.util.ArrayList`.

---

### Trap 3: `ConcurrentHashMap` Disallows `null`
* Unlike standard `HashMap`, `ConcurrentHashMap` throws `NullPointerException` on `null` keys or values.
* **Why:** In concurrent programming, if `map.get(k)` returned `null`, you couldn't tell if the key is missing or mapped to `null` without calling `containsKey(k)`, creating a race condition between the two calls.

---

### Trap 4: Forgetting `Comparable` with `TreeSet` / `TreeMap`
* If you insert custom objects into a `TreeSet` without either implementing `Comparable<T>` or providing a `Comparator`, it compiles fine but throws `ClassCastException` on runtime insertion!

---

### Trap 5: Comparing `TreeSet` using `compareTo()`, NOT `equals()`
* `TreeSet` determines duplicate elements using `compareTo() == 0`, **not `equals()`**.
* If `compareTo()` returns `0` for two objects that are not equal according to `equals()`, `TreeSet` will drop one of them as a duplicate!

---

## 10. How to Answer in an Interview (Exact Wordings)

### Q1: "How does HashMap work internally in Java 8+?"
> *"HashMap uses an array of buckets, where each bucket holds a linked list of key-value nodes. When a key is inserted, its hash code determines the bucket index. If collisions occur, nodes are chained in a list. In Java 8, if any bucket exceeds 8 nodes and the table has at least 64 buckets, that list transforms into a Red-Black Tree, reducing lookup time from O(n) to O(log n)."*

### Q2: "What is the contract between `equals()` and `hashCode()`?"
> *"If two objects are equal according to `equals()`, their `hashCode()` must return the exact same integer value. The inverse is not required: two unequal objects can share the same hash code (a hash collision). If broken, hash-based collections like HashMap and HashSet fail to find stored elements."*

### Q3: "Why would you choose ArrayList over LinkedList in almost all cases?"
> *"ArrayList is backed by a contiguous array, offering O(1) random access and excellent CPU cache locality. LinkedList requires an extra object allocation and two pointers per node, leading to poor cache performance and pointer-chasing overhead that makes even middle insertions slower in practice than shifting array elements."*

### Q4: "What is the difference between Fail-Fast and Fail-Safe iterators?"
> *"Fail-Fast iterators, used by standard collections like ArrayList and HashMap, immediately throw ConcurrentModificationException if the collection is structurally modified during iteration. Fail-Safe (or weakly consistent) iterators, used by concurrent collections like ConcurrentHashMap, operate on a clone or snapshot and never throw an exception when concurrent modifications occur."*

---

## 11. 60-Second Recall Checklist

1. **Hierarchy:** `Collection` (List, Set, Queue) vs. `Map` (separate hierarchy).
2. **List Choice:** Always default to `ArrayList` ($O(1)$ random access, cache-friendly).
3. **Set Choice:** `HashSet` (unordered $O(1)$), `LinkedHashSet` (insertion order), `TreeSet` (sorted $O(\log n)$).
4. **HashMap Treeification:** $\ge 8$ elements in a bucket + table size $\ge 64$ transforms linked list to Red-Black Tree ($O(n) \rightarrow O(\log n)$).
5. **Contract Rule:** If `equals()` is true, `hashCode()` MUST be identical.
6. **Concurrent Choice:** Use `ConcurrentHashMap` (bucket-level locking; no `null` keys or values).
7. **Modifying While Iterating:** Use `Iterator.remove()` or `list.removeIf()`, never standard `for-each`.
8. **Modern Immutable Lists:** Prefer `List.of()` (Java 9+, true immutable, no nulls).
