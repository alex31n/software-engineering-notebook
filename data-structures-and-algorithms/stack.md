# Stack

A Stack is a fundamental data structure that follows the **Last-In, First-Out (LIFO)** principle. Think of it like a stack of plates: you can only add a new plate to the top, and you can only remove the topmost plate.

## What is a Stack?

A Stack is a linear data structure that stores a collection of items. Unlike an array, it restricts data access to only one end, which we call the "top." The last item you add (push) is always the first item you can remove (pop).

This simple constraint makes it incredibly useful for managing tasks, tracking states, and solving certain types of problems.

## The LIFO Principle: An Analogy

Imagine a pile of books on your desk:
1. You place **Book A** down first.
2. Then you place **Book B** on top of it.
3. Finally, you add **Book C** to the very top.

To get to Book A, you must first remove Book C, then Book B. The last book you put on the pile is the first one you have to take off. This is **Last-In, First-Out**.

## Core Operations

A stack is defined by a few simple operations:
- **Push:** Adds a new element to the top of the stack.
- **Pop:** Removes the top element from the stack and returns it.
- **Peek (or Top):** Looks at the top element without removing it.
- **isEmpty:** Checks if the stack is empty.
- **isFull:** Checks if the stack is full (only relevant for fixed-size array implementations).


## Big O Complexity

When implemented correctly (using a dynamic array or linked list), a stack is very efficient for its primary operations.

| Operation | Complexity | Why?                                                              |
|-----------|------------|-------------------------------------------------------------------|
| Push      | $O(1)$     | Adding to the top is a single, direct operation.                  |
| Pop       | $O(1)$     | Removing from the top is also a single operation.                 |
| Peek      | $O(1)$     | Reading the top element is instantaneous.                         |
| Search    | $O(n)$     | You may need to traverse the entire stack to find a specific item. |

## Internal Implementation

Stacks are typically built using one of two underlying structures:

1. **Array-based:** Uses a dynamic array and a `top` pointer (index).
   - **Pros:** Fast access, memory efficient.
   - **Cons:** May require resizing (amortized $O(1)$).
2. **Linked List-based:** Uses a head pointer as the "top."
   - **Pros:** Truly $O(1)$ push/pop (no resizing), grows dynamically.
   - **Cons:** Extra memory for pointers (nodes).

## Implementation

Stacks are commonly implemented using either a dynamic array (like Python's list or Java's ArrayList) or a Linked List.

### Python
In Python, the built-in `list` type makes a great stack. The `append()` method acts as `push`, and `pop()` works as expected.

```python
# Using a list as a stack
browser_history = []

# User visits pages
browser_history.append("google.com")
browser_history.append("github.com")
browser_history.append("stackoverflow.com")

# Peek: Look at the top item without removing it
print(f"Current page: {browser_history[-1]}")

# User hits the back button
last_page = browser_history.pop()
print(f"Went back from: {last_page}")
print(f"Now on page: {browser_history[-1]}")
```

### Java
Java provides a `Stack` class out of the box, which includes all the standard methods.

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<String> undoStack = new Stack<>();

        // User types some words
        undoStack.push("Hello");
        undoStack.push("World");
        undoStack.push("!");

        // Note: Stack (Vector) iterator is bottom-to-top (Insertion order)
        System.out.println("Current text: " + String.join(" ", undoStack));

        // User hits "Undo"
        if (!undoStack.isEmpty()) {
            String lastAction = undoStack.pop();
            System.out.println("Undoing: " + lastAction);
        }
        System.out.println("Text after undo: " + String.join(" ", undoStack));
    }
}
```

> [!NOTE]
> In Java, the `Stack` class is considered legacy. It is recommended to use the `Deque` interface and `ArrayDeque` implementation for better performance, as `Stack` is synchronized and inherits from `Vector`, which adds unnecessary overhead.

#### Using `ArrayDeque` (Recommended)

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ArrayDequeStackExample {
    public static void main(String[] args) {
        // Deque (Double Ended Queue) can be used as a Stack
        Deque<String> undoStack = new ArrayDeque<>();

        // User types some words
        undoStack.push("Hello");
        undoStack.push("World");
        undoStack.push("!");

        // Note: ArrayDeque iterator is top-to-bottom (LIFO order)
        System.out.println("Current stack (top-to-bottom): " + String.join(" ", undoStack));

        // User hits "Undo"
        if (!undoStack.isEmpty()) {
            String lastAction = undoStack.pop();
            System.out.println("Undoing: " + lastAction);
        }
        System.out.println("Stack after undo: " + String.join(" ", undoStack));
    }
}
```

> [!CAUTION]
> Calling `pop()` or `peek()` on an empty stack will throw an exception (e.g., `EmptyStackException` in Java `Stack` or `NoSuchElementException` in `ArrayDeque`). Always check `isEmpty()` first!

## When to Use a Stack

The LIFO behavior of stacks makes them perfect for any situation that involves "backtracking" or reversing order.

- **Function Call Management:** The "call stack" in programming languages keeps track of which function is currently running. When a function is called, it's pushed onto the stack. When it finishes, it's popped off.
- **Undo/Redo Features:** In text editors or image software, each action you take can be pushed onto a stack. Hitting "undo" simply pops the last action off the stack.
- **Browser History:** The "back" button in your web browser uses a stack. Each new page you visit is pushed onto the stack. When you click "back," the current page is popped, revealing the one before it.
- **Backtracking Algorithms:** Problems like solving a maze or a Sudoku puzzle often use a stack to keep track of decision points. If a path leads to a dead end, the algorithm can "pop" back to the last choice and try a different one.