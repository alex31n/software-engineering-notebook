# Breadth-First Search (BFS)

Breadth-First Search (BFS) is a fundamental algorithm used for traversing or searching through data structures like **Trees** and **Graphs**. 

It explores the data structure **level by level**, moving outwards from the starting point. It visits all the direct neighbors of a node before moving on to the neighbors of those neighbors.

## Real-Life Analogy: The Social Network

Imagine you are trying to find a specific person on a social media network (like finding someone who works at a specific company) by asking your connections.

**How BFS does it:**
1.  **Level 1 (Direct Friends):** First, you ask all of your immediate, direct friends. 
2.  **Level 2 (Friends of Friends):** If none of your direct friends are the person you are looking for, you then ask *all* the friends of your direct friends.
3.  **Level 3 (3rd Degree Connections):** If you still haven't found them, you ask the friends of the friends of your friends.

You search broadly (breadth) across your current circle before going deeper into the network. 

*(Note: The opposite approach would be asking one friend, then asking their friend, then asking their friend's friend, going as deep as possible in a single line before checking anyone else. That is called Depth-First Search or DFS).*

## The Secret Ingredient: A Queue

To keep track of which nodes to visit next in a strict "level-by-level" order, BFS relies heavily on a **Queue** (a First-In, First-Out data structure). 

When you visit a node, you look at its neighbors. You add those neighbors to the back of the queue. Because a queue processes the oldest items first, you are guaranteed to finish processing all the nodes on the current level before you start processing the nodes on the next level.

## How It Works (The Logic)

Here is the standard step-by-step logic for BFS:

1.  Pick a starting node and add it to your **Queue**.
2.  Mark this starting node as **Visited** (so you don't get stuck in an infinite loop if the graph has cycles/circles).
3.  **While the Queue is not empty:**
    *   **Dequeue** the node at the front of the queue. This is your `current_node`.
    *   Process the `current_node` (e.g., print its value, check if it's the target).
    *   Look at all the **unvisited neighbors** of the `current_node`.
    *   Mark each of these neighbors as **Visited** and **Enqueue** them (add them to the back of the queue).

## Step-by-Step Example

Let's trace a BFS on a simple graph. 

**The Graph:**
```text
    A
   / \
  B   C
  |   |
  D   E
   \ /
    F
```

**Goal:** Traverse the whole graph starting from **A**.

*   **Setup:** `Queue = []`, `Visited = {}`
*   **Step 1:** Start at **A**. Mark **A** visited. Enqueue **A**.
    *   `Queue = [A]`, `Visited = {A}`
*   **Step 2:** Dequeue **A**. Neighbors of A are **B** and **C**. Mark them visited and enqueue them.
    *   `Queue = [B, C]`, `Visited = {A, B, C}`
*   **Step 3:** Dequeue **B**. Unvisited neighbor of B is **D**. Mark visited, enqueue.
    *   `Queue = [C, D]`, `Visited = {A, B, C, D}`
*   **Step 4:** Dequeue **C**. Unvisited neighbor of C is **E**. Mark visited, enqueue.
    *   `Queue = [D, E]`, `Visited = {A, B, C, D, E}`
*   **Step 5:** Dequeue **D**. Unvisited neighbor of D is **F**. Mark visited, enqueue.
    *   `Queue = [E, F]`, `Visited = {A, B, C, D, E, F}`
*   **Step 6:** Dequeue **E**. Neighbor of E is **F**, but **F is already visited**. Do nothing.
    *   `Queue = [F]`, `Visited = {A, B, C, D, E, F}`
*   **Step 7:** Dequeue **F**. Neighbors D and E are already visited. Do nothing.
    *   `Queue = []` -> **Queue is empty! Search is complete.**

**Final Traversal Order:** A, B, C, D, E, F. 
*(Notice how it printed level 0 (A), then level 1 (B, C), then level 2 (D, E), then level 3 (F)).*

## Complexity

*   **Time Complexity:** $O(V + E)$
    *   `V` is the number of Vertices (nodes). You visit every node exactly once.
    *   `E` is the number of Edges (connections). When visiting a node, you look at all of its outgoing edges. 
    *   Therefore, the time it takes is directly proportional to the size of the graph (nodes + connections).
*   **Space Complexity:** $O(V)$
    *   In the worst-case scenario (e.g., a "star" shaped graph where one node connects to everything else), the Queue could hold almost all the vertices at once. 
    *   You also need memory to keep track of the `Visited` nodes, which grows up to the total number of vertices $V$.

## Implementation

In code, graphs are often represented using an **Adjacency List** (a dictionary/map where the key is the node, and the value is a list of its neighbors).

### Python

Python's `collections.deque` is the perfect tool for our BFS queue because popping from the front is an $O(1)$ operation.

```python
from collections import deque

def bfs(graph, start_node):
    # 1. Create a set to keep track of visited nodes to avoid loops
    visited = set()
    
    # 2. Create a queue and add the starting node
    queue = deque([start_node])
    visited.add(start_node)
    
    result = [] # Just to store the order for printing

    # 3. Loop until the queue is empty
    while queue:
        # Remove the node from the front of the queue
        current_node = queue.popleft()
        result.append(current_node)
        
        # Look at all neighbors of the current node
        for neighbor in graph[current_node]:
            if neighbor not in visited:
                # Mark as visited BEFORE adding to queue to prevent duplicates
                visited.add(neighbor)
                queue.append(neighbor)
                
    return result

# Example Graph (Adjacency List)
# A -> B, C
# B -> D
# C -> E
# D -> F
# E -> F
# F -> (none)
my_graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['E'],
    'D': ['F'],
    'E': ['F'],
    'F': []
}

print("BFS Traversal:", bfs(my_graph, 'A'))
# Output: BFS Traversal: ['A', 'B', 'C', 'D', 'E', 'F']
```

### Java

In Java, we use the `Queue` interface (typically implemented by `LinkedList`) and a `HashSet` to keep track of visited nodes.

```java
import java.util.*;

public class BreadthFirstSearch {

    public static void bfs(Map<String, List<String>> graph, String startNode) {
        Set<String> visited = new HashSet<>();
        Queue<String> queue = new LinkedList<>();

        // Start the process
        visited.add(startNode);
        queue.add(startNode);

        System.out.print("BFS Traversal: ");

        while (!queue.isEmpty()) {
            // Dequeue a vertex from queue
            String currentNode = queue.poll();
            System.out.print(currentNode + " ");

            // Get all neighbors of the dequeued node
            List<String> neighbors = graph.getOrDefault(currentNode, new ArrayList<>());
            for (String neighbor : neighbors) {
                if (!visited.contains(neighbor)) {
                    visited.add(neighbor);
                    queue.add(neighbor);
                }
            }
        }
    }

    public static void main(String[] args) {
        Map<String, List<String>> graph = new HashMap<>();
        graph.put("A", Arrays.asList("B", "C"));
        graph.put("B", Arrays.asList("D"));
        graph.put("C", Arrays.asList("E"));
        graph.put("D", Arrays.asList("F"));
        graph.put("E", Arrays.asList("F"));
        graph.put("F", new ArrayList<>()); // No outgoing edges

        bfs(graph, "A"); 
        // Output: BFS Traversal: A B C D E F 
    }
}
```

## When to Use BFS

Because of its level-by-level nature, BFS is the ultimate tool for finding the **Shortest Path** in an unweighted graph (a graph where every connection takes the same amount of time/distance).

Common use cases include:
-   **GPS Navigation:** Finding the route with the fewest number of turns or intersections.
-   **Social Networks:** Finding "Degrees of Separation" (e.g., how many connections away is Person X from Person Y?).
-   **Web Crawlers:** Google's bots use BFS to build web indexes. They start at a source page, visit all the links on that page, then visit all the links on *those* pages, and so on.
-   **Broadcasting in Networks:** Routing packets in a peer-to-peer network to reach all nodes.

## Example: Finding the Shortest Path

Here is how you can modify the standard BFS to not just traverse, but find the shortest path (the minimum number of edges) between a `start_node` and a `target_node`. We do this by keeping track of the path taken to reach each node alongside the node itself in the queue.

### Python Shortest Path Example

```python
from collections import deque

def shortest_path_bfs(graph, start_node, target_node):
    # Queue stores tuples of (node, path_to_reach_node)
    queue = deque([(start_node, [start_node])])
    visited = {start_node}
    
    while queue:
        current_node, path = queue.popleft()
        
        # If we reached our target, return the path that got us here
        if current_node == target_node:
            return path
            
        for neighbor in graph.get(current_node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                # Append the neighbor to the current path to form the new path
                new_path = path + [neighbor]
                queue.append((neighbor, new_path))
                
    return None # Target not reachable

# Example usage using the same graph from before:
# A -> B, C
# B -> D
# C -> E
# D -> F
# E -> F
# F -> (none)
my_graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['E'],
    'D': ['F'],
    'E': ['F'],
    'F': []
}

print("Shortest path from A to F:", shortest_path_bfs(my_graph, 'A', 'F'))
# Output: Shortest path from A to F: ['A', 'B', 'D', 'F']
# Note: ['A', 'C', 'E', 'F'] is also a valid shortest path.
```

### Java Shortest Path Example

In Java, we can store a custom object or use a mapping of nodes to their parent nodes to reconstruct the path once the target is found.

```java
import java.util.*;

public class ShortestPathBFS {

    public static List<String> findShortestPath(Map<String, List<String>> graph, String startNode, String targetNode) {
        Queue<String> queue = new LinkedList<>();
        Set<String> visited = new HashSet<>();
        
        // Map to keep track of the path: childNode -> parentNode
        Map<String, String> parentMap = new HashMap<>();

        queue.add(startNode);
        visited.add(startNode);
        
        boolean found = false;

        while (!queue.isEmpty()) {
            String currentNode = queue.poll();
            
            if (currentNode.equals(targetNode)) {
                found = true;
                break;
            }
            
            List<String> neighbors = graph.getOrDefault(currentNode, new ArrayList<>());
            for (String neighbor : neighbors) {
                if (!visited.contains(neighbor)) {
                    visited.add(neighbor);
                    parentMap.put(neighbor, currentNode);
                    queue.add(neighbor);
                }
            }
        }
        
        if (!found) {
            return Collections.emptyList(); // Path not found
        }
        
        // Reconstruct the path by backtracking from target to start
        List<String> path = new ArrayList<>();
        String curr = targetNode;
        while (curr != null) {
            path.add(curr);
            curr = parentMap.get(curr);
        }
        
        Collections.reverse(path);
        return path;
    }

    public static void main(String[] args) {
        Map<String, List<String>> graph = new HashMap<>();
        graph.put("A", Arrays.asList("B", "C"));
        graph.put("B", Arrays.asList("D"));
        graph.put("C", Arrays.asList("E"));
        graph.put("D", Arrays.asList("F"));
        graph.put("E", Arrays.asList("F"));
        graph.put("F", new ArrayList<>());

        List<String> path = findShortestPath(graph, "A", "F");
        System.out.println("Shortest path from A to F: " + path);
        // Output: Shortest path from A to F: [A, B, D, F]
    }
}
```