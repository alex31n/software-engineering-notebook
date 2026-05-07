# Queue

A **Queue** is a linear data structure that follows the **First-In, First-Out (FIFO)** principle. This means that the first element added to the queue will be the first one to be removed.

## What is a Queue?

Imagine a line of people waiting at a ticket counter. The first person to join the line is the first person to get a ticket and leave. A queue in programming works exactly the same way. New items are added to one end (the **rear** or **tail**), and items are removed from the other end (the **front** or **head**).

This FIFO behavior makes queues perfect for managing tasks, requests, or any data that needs to be processed in the order it was received.

A queue has two main operations:
1.  **Enqueue:** Adds an element to the back (rear) of the queue.
2.  **Dequeue:** Removes an element from the front of the queue.

## How It Works

A queue is managed with two pointers:
-   **Front:** Points to the beginning of the queue (where items are removed).
-   **Rear:** Points to the end of the queue (where items are added).

Let's see it in action:
1.  **Initial State:** The queue is empty. `Front` and `Rear` are at the same position.  
```text
    [ ]
```  
2.  **Enqueue("A"):** Add "A". `Rear` moves.  
```text
    [ "A" ]  
       ^  
    Front/Rear
```  
1.  **Enqueue("B"):** Add "B". `Rear` moves again.
```text  
    [ "A",  "B" ]  
       ^     ^  
     Front  Rear  
```
2.  **Dequeue():** Remove an item. The item at the `Front` ("A") is removed, and `Front` moves.  
```text
    [ "B" ]  
       ^  
    Front/Rear  
```

## Big O Complexity

Queues are highly efficient for their primary operations.
-   **Enqueue (Insertion):** $O(1)$ — Adding an element to the end is a single operation.
-   **Dequeue (Deletion):** $O(1)$ — Removing an element from the front is also a single operation.
-   **Peek (Accessing the front element):** $O(1)$ — Just looking at the front element is instant.
-   **Search:** $O(n)$ — To find an element in the middle, you would have to look through the queue one by one from the front.

## Implementation

Queues can be implemented using arrays or linked lists. While arrays can work, they are inefficient for queues because removing an element from the beginning requires shifting all other elements, which is an $O(n)$ operation. A **Linked List** is a much better choice because adding or removing from either end is an $O(1)$ operation.

Most modern languages provide a built-in, highly optimized queue implementation.

### Python
In Python, the `collections.deque` (double-ended queue) object is the perfect tool for implementing a queue efficiently.

```python
from collections import deque

# Create a new queue
my_queue = deque()

# Enqueue: Add items to the right side
my_queue.append("Task 1")
my_queue.append("Task 2")
my_queue.append("Task 3")

print(f"Queue after enqueuing: {my_queue}")

# Dequeue: Remove an item from the left side
first_item = my_queue.popleft()
print(f"Dequeued item: {first_item}")
print(f"Queue after dequeuing: {my_queue}")

# Peek: Look at the front item without removing it
if my_queue:
    print(f"Front item is: {my_queue}")
```

### Java
In Java, the `Queue` is an interface, and it's commonly implemented using the `LinkedList` class.

```java
import java.util.LinkedList;
import java.util.Queue;

public class QueueExample {
    public static void main(String[] args) {
        // Create a new queue using LinkedList
        Queue<String> myQueue = new LinkedList<>();

        // Enqueue: Add items to the queue
        myQueue.add("Job A");
        myQueue.add("Job B");
        myQueue.add("Job C");

        System.out.println("Current queue: " + myQueue);

        // Dequeue: Remove an item from the front
        String processedJob = myQueue.remove();
        System.out.println("Processed: " + processedJob);
        System.out.println("Queue after removal: " + myQueue);

        // Peek: Look at the front item without removing it
        String nextJob = myQueue.peek();
        System.out.println("Next job in line is: " + nextJob);
    }
}
```

## When to Use a Queue

Queues are essential in a wide variety of programming scenarios:
-   **Task Scheduling:** A printer queue that processes print jobs in the order they were received.
-   **Breadth-First Search (BFS):** An algorithm for traversing trees or graphs, which explores all neighbor nodes at the present depth prior to moving on to nodes at the next depth level.
-   **Buffering:** Handling data that comes in bursts. For example, streaming video data is placed in a queue so it can be played back smoothly, even if the network connection is temporarily unstable.
-   **Order Processing:** In an e-commerce system, incoming orders are placed in a queue to be processed one by one.