# Recursion

Recursion is a powerful programming technique where a function calls itself to solve a problem. Instead of using loops, a recursive function breaks a problem down into smaller, identical subproblems until it reaches a simple case that can be solved directly.

## What is Recursion?

In recursion, a function calls itself, either directly or indirectly, to solve a problem. This approach is useful for problems that can be broken down into smaller, self-similar instances.

Let's look at a couple of analogies to understand the concept.

**Analogy 1: Russian Nesting Dolls**

Imagine a set of Matryoshka (Russian nesting) dolls. To find the smallest doll, you open the current doll to reveal a smaller one inside. You repeat this process—opening each new doll—until you find one that doesn't open anymore. That's your final doll. This process of repeating the same action on a smaller version of the object is the core idea of recursion.

**Analogy 2: Finding a File in a Folder**

Another way to think about it is searching for a file within a directory structure. To find a specific file, you look through the contents of a folder. If you don't find it, you then search inside every subfolder. For each subfolder, you perform the exact same action: search its contents, and if necessary, search *its* subfolders. This continues until you either find the file or have checked all folders.

A recursive function always has two main parts:
1.  **Base Case:** This is the simplest version of the problem, which has a direct answer and stops the recursion. Without a base case, the function would call itself forever, leading to a "stack overflow" error.
    - In the doll analogy, the base case is the doll that won't open.
    - In the file search analogy, the base case is finding the file or reaching an empty folder.
2.  **Recursive Step:** This is where the function calls itself with a modified input, bringing it one step closer to the base case.
    - In the doll analogy, this is opening a doll to get the next smaller one.
    - In the file search analogy, this is opening a subfolder to search within it.

## How It Works: The Call Stack

Recursion uses a data structure called the **call stack** to manage function calls. When a function is called, a new "stack frame" is pushed onto the top of the stack, containing that function's local variables and parameters.

When a recursive function calls itself, another frame is pushed onto the stack. This continues until the base case is reached. Once the base case returns a value, its frame is popped off the stack, and the previous function call resumes its execution, using the returned value. This "unwinding" process continues until the very first function call is completed.

## Step-by-Step Example: Factorial

The factorial of a number `n` (written as `n!`) is the product of all positive integers up to `n`. For example, `5! = 5 * 4 * 3 * 2 * 1 = 120`.

We can define this recursively:
- **Recursive Step:** `factorial(n) = n * factorial(n-1)`
- **Base Case:** `factorial(0) = 1`

Let's trace `factorial(3)`:
1.  `factorial(3)` is called. It needs `factorial(2)`, so it calls itself with `2`.
2.  `factorial(2)` is called. It needs `factorial(1)`, so it calls itself with `1`.
3.  `factorial(1)` is called. It needs `factorial(0)`, so it calls itself with `0`.
4.  `factorial(0)` is called. This is the **base case**, and it returns `1`.
5.  `factorial(1)` receives `1`, calculates `1 * 1`, and returns `1`.
6.  `factorial(2)` receives `1`, calculates `2 * 1`, and returns `2`.
7.  `factorial(3)` receives `2`, calculates `3 * 2`, and returns `6`.

## Complexity

*   **Time Complexity:** The time complexity depends on the number of recursive calls. For a factorial function, it makes `n` calls, so the complexity is $O(n)$.
*   **Space Complexity:** The space complexity is determined by the maximum depth of the call stack. For a factorial function, the stack goes `n` levels deep, resulting in a space complexity of $O(n)$.

## Implementation

### Python
```python
def factorial(n):
    # Input validation: Factorial is not defined for negative numbers
    if n < 0:
        raise ValueError("Factorial is not defined for negative numbers")
    
    # Base Case: The simplest version of the problem (0! = 1). This stops the recursion.
    elif n == 0:
        return 1
        
    # Recursive Step: Break the problem down into a smaller piece.
    # The function calls itself with a smaller input (n - 1).
    else:
        return n * factorial(n - 1)

# Example usage:
print(f"The factorial of 5 is: {factorial(5)}") # Output: 120
```

### Java
```java
public class RecursionExample {

    public static long factorial(int n) {
        // Input validation: Factorial is not defined for negative numbers
        if (n < 0) {
            throw new IllegalArgumentException("Factorial is not defined for negative numbers.");
        }
        // Base Case: The simplest version of the problem (0! = 1). This stops the recursion.
        else if (n == 0) {
            return 1;
        }
        // Recursive Step: Break the problem down into a smaller piece.
        // The function calls itself with a smaller input (n - 1).
        else {
            return n * factorial(n - 1);
        }
    }

    public static void main(String[] args) {
        int number = 5;
        long result = factorial(number);
        System.out.println("The factorial of " + number + " is: " + result); // Output: 120
    }
}
```

## Recursion vs. Iteration

| Aspect           | Recursion                                                                                              | Iteration                                                                                       |
| ---------------- | ------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------- |
| **Approach**     | A function calls itself.                                                                               | Uses loops (`for`, `while`) to repeat code.                                                     |
| **Code**         | Often more concise and readable for problems that are naturally recursive (e.g., tree traversals). | Can be more verbose but is often more straightforward for linear tasks.                         |
| **Performance**  | Can be slower due to the overhead of function calls.                                                   | Generally faster as it avoids function call overhead.                                           |
| **Memory Usage** | Uses more memory due to the call stack growing with each call.                                         | Uses a constant amount of memory (for loop variables).                                          |
| **Termination**  | Stops when a base case is reached. A missing base case causes a stack overflow.                       | Stops when the loop condition becomes false. An incorrect condition can cause an infinite loop. |

## When to Use Recursion

Recursion is an excellent choice for problems that can be broken down into smaller, identical subproblems. It is particularly well-suited for:

- **Tree and Graph Traversals:** Algorithms like Depth-First Search (DFS) are often implemented recursively. 
- **Divide and Conquer Algorithms:** Problems like Merge Sort and Quick Sort use recursion to divide the data, sort the smaller parts, and then combine them.
- **Backtracking Problems:** Recursion is used to explore all possible solutions and "backtrack" if a path is not viable.

While any recursive algorithm can be rewritten iteratively (often using a stack), the recursive version is sometimes more intuitive and easier to understand.