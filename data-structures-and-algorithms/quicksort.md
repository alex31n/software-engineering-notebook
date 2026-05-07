# Quicksort 

Quicksort is a highly efficient, comparison-based sorting algorithm that uses the **Divide and Conquer** strategy. It works by selecting a 'pivot' element from the array and partitioning the other elements into two sub-arrays, according to whether they are less than or greater than the pivot.

Imagine you're organizing a messy pile of test papers by score. You pick a random test (the pivot). You put all tests with lower scores to the left, and all tests with higher scores to the right. Now, the pivot test is perfectly in its final place! You then repeat this exact same process for the smaller pile on the left and the smaller pile on the right until all papers are sorted.

## How it Works
1. **Choose a Pivot:** Select an element from the list to act as a pivot point. There are many ways to pick a pivot (e.g., first element, last element, random, or median).
2. **Partitioning:** Rearrange the list so that all elements smaller than the pivot come before it, and all elements larger than the pivot come after it. The pivot is now in its final sorted position.
3. **Recursive Sort:** Recursively apply the above steps to the sub-array of elements with smaller values and the sub-array of elements with larger values.
4. **Base Case:** The recursion stops when the sub-arrays have zero or one element (as they are inherently sorted).

## Step-by-Step Example
Let's sort the list: `[10, 80, 30, 90, 40, 50, 70]`

* **Step 1:** Let's pick the last element, `70`, as our **pivot**.
* **Step 2 (Partitioning):** We compare elements with `70`.
  * `10` is less than `70` (Left side)
  * `80` is greater than `70` (Right side)
  * `30` is less than `70` (Left side)
  * `90` is greater than `70` (Right side)
  * `40` is less than `70` (Left side)
  * `50` is less than `70` (Left side)
  * *After Partitioning:* `[10, 30, 40, 50,` **`70`**`, 90, 80]` (Notice `70` is now perfectly in the correct spot).
* **Step 3 (Recursion):** Now we apply Quicksort to the left part `[10, 30, 40, 50]` and the right part `[90, 80]`.
* **Step 4 (Left Part):** Pick `50` as pivot. After partitioning, it remains `[10, 30, 40, 50]`. This part is sorted.
* **Step 5 (Right Part):** Pick `80` as pivot. `90` goes to the right. After partitioning, we get `[80, 90]`.
* **Final Result:** Combine everything: `[`**`10, 30, 40, 50, 70, 80, 90`**`]`

## Complexity
* **Time Complexity:** 
  * **Best & Average Case:** $O(n \log n)$. This happens when the pivot element consistently divides the array into roughly equal halves.
  * **Worst Case:** $O(n^2)$. This occurs when the pivot chosen is consistently the greatest or smallest element (e.g., trying to sort an already sorted list with the last element as pivot). Good pivot choices (like random or median) almost eliminate this risk.
* **Space Complexity:** $O(\log n)$ on average due to recursive call stack. In the worst case, it can be $O(n)$. It is an "in-place" sort, meaning it doesn't need to create entirely new arrays during the process.

## Algorithm Steps
1. Determine the base case: If the array has 1 or fewer elements, it is sorted. Return it.
2. Pick a pivot element from the array.
3. Create two pointers (or variables) to track elements smaller and larger than the pivot.
4. Traverse the array and swap elements to ensure smaller elements are placed before the pivot and larger elements are placed after it.
5. Recursively call Quicksort on the left and right sub-partitions.

## Implementation

To provide a complete understanding, we have included both a **Basic (Out-of-place)** and an **Advanced (In-place)** implementation for both Python and Java.

*   **Basic (Out-of-place):** Uses extra lists to clearly demonstrate the concept of partitioning. Great for beginners but uses more memory ($O(n)$ space).
*   **Advanced (In-place):** Uses pointers to swap elements within the original array without creating new lists. This is how Quicksort is typically implemented in the real world to save memory ($O(\log n)$ space).

### Python

#### 1. Basic Implementation (For Clarity)
```python
def quicksort_basic(arr):
    # Base case: arrays with 0 or 1 element are already sorted
    if len(arr) <= 1:
        return arr
    else:
        # Choose a pivot (we'll use the last element here)
        pivot = arr[-1]
        
        # Partitioning using extra lists for clarity
        less_than_pivot = []
        greater_than_pivot = []
        
        # Iterate through everything except the pivot
        for item in arr[:-1]:
            if item <= pivot:
                less_than_pivot.append(item)
            else:
                greater_than_pivot.append(item)
                
        # Recursively sort and combine
        return quicksort_basic(less_than_pivot) + [pivot] + quicksort_basic(greater_than_pivot)

# Example usage:
numbers = [10, 80, 30, 90, 40, 50, 70]
sorted_numbers = quicksort_basic(numbers)
print(f"Basic Sorted list: {sorted_numbers}")
```

#### 2. Advanced Implementation (In-place with Pointers)
```python
def quicksort_inplace(arr, low, high):
    if low < high:
        # Find the pivot index after partitioning
        pivot_index = partition(arr, low, high)
        
        # Recursively sort elements before and after partition
        quicksort_inplace(arr, low, pivot_index - 1)
        quicksort_inplace(arr, pivot_index + 1, high)

def partition(arr, low, high):
    # Choosing the last element as the pivot
    pivot = arr[high]
    i = low - 1  # Index of smaller element
    
    for j in range(low, high):
        # If current element is smaller than or equal to pivot
        if arr[j] <= pivot:
            i += 1
            # Swap arr[i] and arr[j]
            arr[i], arr[j] = arr[j], arr[i]
            
    # Swap arr[i+1] and the pivot (arr[high])
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    
    return i + 1

# Example usage:
numbers = [10, 80, 30, 90, 40, 50, 70]
quicksort_inplace(numbers, 0, len(numbers) - 1)
print(f"In-place Sorted list: {numbers}")
```

### Java

#### 1. Basic Implementation (For Clarity)
```java
import java.util.ArrayList;
import java.util.List;

public class QuickSortBasic {
    public static List<Integer> quickSort(List<Integer> arr) {
        // Base case: arrays with 0 or 1 element are already sorted
        if (arr.size() <= 1) {
            return arr;
        }

        // Choose a pivot (last element)
        int pivot = arr.get(arr.size() - 1);
        
        // Partitioning using extra lists
        List<Integer> lessThanPivot = new ArrayList<>();
        List<Integer> greaterThanPivot = new ArrayList<>();

        // Iterate through everything except the pivot
        for (int i = 0; i < arr.size() - 1; i++) {
            if (arr.get(i) <= pivot) {
                lessThanPivot.add(arr.get(i));
            } else {
                greaterThanPivot.add(arr.get(i));
            }
        }

        // Recursively sort and combine
        List<Integer> sorted = new ArrayList<>(quickSort(lessThanPivot));
        sorted.add(pivot);
        sorted.addAll(quickSort(greaterThanPivot));

        return sorted;
    }

    public static void main(String[] args) {
        List<Integer> numbers = List.of(10, 80, 30, 90, 40, 50, 70);
        System.out.println("Basic Sorted list: " + quickSort(new ArrayList<>(numbers)));
    }
}
```

#### 2. Advanced Implementation (In-place with Pointers)
```java
public class QuickSortInPlace {

    public static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            // Find the pivot index after partitioning
            int pivotIndex = partition(arr, low, high);

            // Recursively sort elements before and after partition
            quickSort(arr, low, pivotIndex - 1);
            quickSort(arr, pivotIndex + 1, high);
        }
    }

    // Partitioning logic
    private static int partition(int[] arr, int low, int high) {
        // Choosing the last element as the pivot
        int pivot = arr[high];
        int i = (low - 1); // Index of smaller element

        for (int j = low; j < high; j++) {
            // If current element is smaller than the pivot
            if (arr[j] < pivot) {
                i++;
                // Swap arr[i] and arr[j]
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }

        // Swap arr[i+1] and the pivot (arr[high])
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;

        return i + 1;
    }

    // Example usage:
    public static void main(String[] args) {
        int[] data = {10, 80, 30, 90, 40, 50, 70};
        quickSort(data, 0, data.length - 1);
        
        System.out.print("In-place Sorted Array: ");
        for (int num : data) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}
```
## Advantages & Disadvantages
**Advantages:**
- Very fast in practice and widely used (often faster than Merge Sort).
- Cache-friendly because it works in-place and accesses memory sequentially.
- Requires little additional memory space ($O(\log n)$ average space complexity).

**Disadvantages:**
- The worst-case time complexity is $O(n^2)$, although this is rare with good pivot selection.
- It is not a stable sort by default (meaning the relative order of duplicate items might change).
