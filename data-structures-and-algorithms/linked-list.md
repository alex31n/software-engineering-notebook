# Linked Lists
While arrays are great for storing data, they have a major flaw: rigidity. An array always stores data in contiguous memory locations. If you run out of space, you often have to move the entire array to a new location in memory, which is slow.
With Linked Lists, your items can be stored anywhere in memory.

## What is a Linked List?
A linked list is a linear data structure where elements are not stored in contiguous memory locations. Instead, each element is a separate object called a `Node`. Each `Node` can be store anywhere in memory.

Each Node contains two parts:
- **Data:** The actual value (number, string, etc.).
- **Next:** A pointer (or reference) to the next node in the sequence.
The list starts with a pointer called the `Head`. The last node points to `NULL`, signifying the end of the list.

## A Simple Example: The Treasure Hunt
Imagine a treasure hunt. You don't have a map showing every location at once. Instead:
- You start at the first location (the `Head`).
- At that location, you find a prize (the Data) and a clue that tells you where the next location is (the `Pointer`).
- You follow the clues one by one. You cannot skip to the fifth location because you don't know where it is until you reach the fourth one.
- The hunt ends when you reach a location that has no more clues (the `Null pointer`).

In code (Conceptual):
- Node A: Data: "Apple", Next: Node B
- Node B: Data: "Banana", Next: Node C
- Node C: Data: "Cherry", Next: NULL

## Big O Complexity
Linked lists trade fast access for fast insertions and deletions.
**Access:** $O(n)$ — You cannot jump to a specific index. You must start at the `Head` and follow the `Next` pointers one by one.
**Search:** $O(n)$ — You must traverse the list until you find the value.
**Insertion/Deletion:** $O(1)$ — This is where Linked Lists faster. If you already have a pointer to the location, you just need to update a couple of `Next` pointers. No element shifting is required!

### Types of Linked Lists
- **Singly Linked List:** Each node points only to the next node.
- **Doubly Linked List:** Each node has two pointers, one to the next node and one to the previous node. This allows you to travel backward.
- **Circular Linked List:** The last node points back to the Head instead of NULL.

**1. Python (Using Classes)**  
Python doesn't have a built-in linked list, so we define a class for the Node.

```Python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

head = Node("Apple")
head.next = Node("Banana")
```

**2. Java (Using Classes)**
In Java, we define a class to represent the structure:
```java
class Node {
    int data;
    Node next;

    Node(int data) {
        this.data = data;
        this.next = null;
    }
}

Node first = new Node(10);
Node second = new Node(20);

first.next = second;
```

**3. JavaScript (Using Objects)**  
In JS, we use objects and references.

```JavaScript
const list = {
    value: "Apple",
    next: {
        value: "Banana",
        next: null
    }
};
```

**4. C++ (Using Pointers)**  
C++ gives you manual control over memory, which is great for understanding how pointers actually work.

```C++
struct Node {
    int data;
    Node* next;
};
```

### When to Use a Linked List
A Linked List is better than an array when:
1. You don't know the final size of the data beforehand.
2. You need to perform frequent insertions or deletions (especially at the beginning).
3. You have a very low number of "read" (access) operations compared to "write" operations.