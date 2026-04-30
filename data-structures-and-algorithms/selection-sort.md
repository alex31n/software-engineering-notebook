# Selection Sort 

Selection Sort is a straightforward, comparison-based sorting algorithm. It works by repeatedly finding the minimum element from the unsorted part of a list and moving it to the beginning.

Imagine you have a playlist of songs, and you want to order them by their view or listening count, from lowest to highest. You would scan the whole playlist, find the song with the absolute lowest view count, and swap it with the song at the very top of the list. Then, you look at all the remaining songs, find the one with the next lowest listening count, and put it in the second spot. You keep doing this until the entire playlist is sorted.

## How it Works
Think of the list as having two parts: the **sorted part** (on the left) and the **unsorted part** (on the right). Initially, the sorted part is empty, and the entire list is unsorted.

1. **Find the smallest:** Look through the unsorted part to find the lowest value.
2. **Swap:** Swap that lowest value with the first element of the unsorted part.
3. **Expand:** Now, the sorted part includes that element. Move the boundary one step to the right.
4. **Repeat:** Continue this process until the entire list is sorted.

## Step-by-Step Example
Let’s sort the list: `[29, 10, 14, 37, 13]`

* **Step 1:** The smallest number is `10`. Swap it with the first element, `29`.
  * *List becomes:* `[`**`10`**`, 29, 14, 37, 13]`
* **Step 2:** In the remaining unsorted part `[29, 14, 37, 13]`, the smallest is `13`. Swap it with `29`.
  * *List becomes:* `[`**`10, 13`**`, 14, 37, 29]`
* **Step 3:** In the unsorted part `[14, 37, 29]`, the smallest is `14`. It is already in the right place, so no swap is needed.
  * *List becomes:* `[`**`10, 13, 14`**`, 37, 29]`
* **Step 4:** In the unsorted part `[37, 29]`, the smallest is `29`. Swap it with `37`.
  * *List becomes:* `[`**`10, 13, 14, 29`**`, 37]`
* **Final Step:** Only `37` is left, which means the list is fully sorted.
  * *Final Result:* `[`**`10, 13, 14, 29, 37`**`]`

## Complexity
* **Time Complexity:** $O(n^2)$. Because Selection Sort runs a nested loop (scanning the remaining numbers repeatedly), the execution time increases drastically as the array size grows. It is generally slow for large datasets.
* **Space Complexity:** $O(1)$. It is an "in-place" sorting algorithm, meaning it requires a constant amount of extra memory regardless of the list size.

## Algorithm Steps
1. Set the first element as the minimum (`min_index`).
2. Compare this minimum with the next element. If the next element is smaller, update the `min_index`.
3. Continue scanning until the end of the array is reached.
4. Swap the minimum element found with the first element of the unsorted section.
5. Increment the starting position of the unsorted section and repeat the steps until the entire array is sorted.

## Implementation
Python
```python
def selection_sort(arr):
    n = len(arr)
    
    for i in range(n):
        # Assume the current position is the minimum
        min_index = i
        
        # Check the rest of the array for a smaller number
        for j in range(i + 1, n):
            if arr[j] < arr[min_index]:
                min_index = j
        
        # Swap the found minimum element with the first element of the unsorted part
        arr[i], arr[min_index] = arr[min_index], arr[i]
        
    return arr

# Example usage:
numbers = [29, 10, 14, 37, 13]
sorted_numbers = selection_sort(numbers)
print(f"Sorted list: {sorted_numbers}")

```

Java
```java
public class SelectionSort {
    
    public static void selectionSort(int[] arr) {
        int n = arr.length;

        // Move the boundary of the unsorted subarray
        for (int i = 0; i < n - 1; i++) {
            // Find the minimum element in the unsorted array
            int minIndex = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }

            // Swap the found minimum element with the first element of the unsorted part
            int temp = arr[minIndex];
            arr[minIndex] = arr[i];
            arr[i] = temp;
        }
    }

    // Example usage:
    public static void main(String[] args) {
        int[] data = {29, 10, 14, 37, 13};
        selectionSort(data);
        
        System.out.print("Sorted Array: ");
        for (int num : data) {
            System.out.print(num + " ");
        }
    }
}
```

## Advantages & Disadvantages
**Advantages:**
- Simple to understand and implement.
- Performs well on very small datasets.
- Requires no additional memory space (In-place sort).
- Minimizes the total number of swaps (maximum $O(n)$ swaps), which is beneficial if writing to memory is an expensive operation.

**Disadvantages:**
- Highly inefficient for large datasets due to the $O(n^2)$ time complexity.
- It is not a stable sort by default (meaning it does not preserve the original relative order of items with duplicate values).

