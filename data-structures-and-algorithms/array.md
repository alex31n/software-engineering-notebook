# Arrays
Sometimes you need to store a list of elements in memory. For example, if you want to store a series of numbers, a single variable isn't enough; you need an array.

## What is an Array?
An array is a collection of items stored at contiguous (sequential) memory locations. This means elements are placed right next to each other in RAM. By storing items of the same type together, the computer can easily calculate the position of any element. The position of an element is called its index.

Imagine you are storing the scores of five players in a game:
scores = [85, 92, 78, 90, 88]
In this array:
- The index starts at 0. So, scores[0] is 85 and scores[4] is 88,.
- The length of the array is 5.
- Each score is stored in the next available memory slot.

## Big O Complexity
To understand how efficient arrays are, we look at their Big O notation:  
- **Read:** $O(1)$ — Since memory is contiguous, the mechine can jump directly to any index instantly.  
- **Search:** $O(n)$ — In an unsorted array, you might have to check every single element to find the one you are looking for.  
- **Insertion/Deletion:** $O(n)$ — This is the main drawback. If you insert or delete an element at the beginning or middle, every subsequent element must "shift" to maintain the contiguous structure.

## What is a Dynamic Array?
A standard static array has a fixed size. If you declare an array for 5 elements and try to add a 6th, the program will throw an error or crash.

A Dynamic Array solves this by automatically resizing itself. When it reaches full capacity:

- It allocates a new, larger array (usually double the size).
- It copies all existing elements to the new array.
- It adds the new element and deletes the old, smaller array.

## Handling Arrays in Different Languages
While the concept remains the same, different programming languages handle arrays and dynamic arrays using different names and syntax:

**1. Java**  
Java uses standard brackets for static arrays and the ArrayList class for dynamic behavior.

```Java
// Static Array
int[] staticArray = new int[5]; 

// Dynamic Array
ArrayList<Integer> dynamicArray = new ArrayList<>();
dynamicArray.add(90);
```

**2. Python**  
In Python, the built-in list is dynamic by default. You don't need to worry about fixed sizes.

```Python
# Everything is dynamic
my_list = [1, 2, 3]
my_list.append(4) # Automatically resizes
```

**3. C++**  
C++ use native arrays for static needs and std::vector for dynamic ones.

```C++
// Static
int arr[5];

// Dynamic
std::vector<int> vec;
vec.push_back(10);
```

## The Final Things
Arrays are incredibly fast for accessing data but can be slow when you need to add or remove items frequently.