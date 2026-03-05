# Binary Search
Binary Search is a fast searching algorithm used to find a target value inside a sorted list (array).

It works by repeatedly dividing the search space into half.

Instead of checking every element one by one (like Linear Search), Binary Search:

1. Checks the middle element
2. If the target is smaller → search the left half
3. If the target is bigger → search the right half
4. Repeat until found (or no elements left)


**Important Rule**

Binary Search only works on sorted data.  
Example of sorted array: `[10, 20, 30, 40, 50, 60, 70]`

## Real-Life Examples

**1. Phonebook Search**

Imagine searching for "Rahim" in a phonebook.  
Phonebooks are sorted alphabetically.   
Steps:
- Open the middle page.
- If names start with M and you need R → go to right half.
- Again open middle of right half.
- Repeat.

Instead of checking 1000 names one by one, you quickly divide the search space.

This is Binary Search!

**2. Username Search in Database**  
Suppose usernames are stored in sorted order:  
["adam", "alex", "bob", "charlie", "david", "rahim", "zara"]  

Searching for "rahim":
- Check middle → "charlie"
- "rahim" > "charlie" → search right half
- Check middle of right → "rahim" → Found ✅

## Algorithm Logic
```
1. Start = 0
2. End = array length - 1

3. While Start <= End:
      Mid = (Start + End) / 2
      
      If array[Mid] == target:
            return Mid
      
      If target < array[Mid]:
            End = Mid - 1
      Else:
            Start = Mid + 1

4. If not found → return -1
```

## Time Complexity
| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | O(1)            |
| Average Case | O(log n)        |
| Worst Case   | O(log n)        |

Why?  
Because every step cuts the search space in half.  
Example:
| Elements  | Max Steps |
| --------- | --------- |
| 8         | 3         |
| 16        | 4         |
| 32        | 5         |
| 1,000,000 | ~20       |


## Implementation

**Python**
~~~python
def binary_search(arr, target):
    start = 0
    end = len(arr) - 1

    while start <= end:
        mid = start + (end - start) // 2

        if arr[mid] == target:
            return mid

        if target < arr[mid]:
            end = mid - 1
        else:
            start = mid + 1

    return -1


arr = [10, 20, 30, 40, 50, 60, 70]
result = binary_search(arr, 60)

if result != -1:
    print("Found at index:", result)
else:
    print("Not found")
~~~

**Java**
~~~java
public class BinarySearchExample {

    public static int binarySearch(int[] arr, int target) {
        int start = 0;
        int end = arr.length - 1;

        while (start <= end) {
            int mid = start + (end - start) / 2; // safer way

            if (arr[mid] == target) {
                return mid;
            }

            if (target < arr[mid]) {
                end = mid - 1;
            } else {
                start = mid + 1;
            }
        }

        return -1; // not found
    }

    public static void main(String[] args) {
        int[] arr = {10, 20, 30, 40, 50, 60, 70};

        int result = binarySearch(arr, 60);

        if (result != -1)
            System.out.println("Found at index: " + result);
        else
            System.out.println("Not found");
    }
}
~~~