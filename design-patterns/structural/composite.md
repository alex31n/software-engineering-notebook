# Composite Design Pattern

The **Composite Design Pattern** is a structural pattern that allows you to compose objects into tree structures to represent part-whole hierarchies. It lets clients treat individual objects and compositions of objects uniformly. 

In simple terms: It is a pattern that lets you group smaller objects into larger groups, and then interact with both the individual objects and the large groups using the exact same interface.

## Analogy

Imagine you are packing to move to a new house. You have items like books, plates, and clothes. You put these items into small boxes. Then, you put those small boxes into bigger boxes. 

If someone asks you, "What is the total weight of this big box?", you don't just weigh the cardboard. You have to weigh all the smaller boxes inside it, and to do that, you have to weigh all the individual items inside *those* boxes. 

In software engineering, the **Composite Design Pattern** helps you handle exactly this kind of situation. It is a **structural design pattern** that lets you compose objects into **tree structures** and then work with these structures as if they were individual objects. 

It allows you to treat individual items (like a book) and groups of items (like a box of books) in the exact same way.

## The Problem

Imagine you are building a File System. A file system consists of:
1. **Files** (individual items with a certain size).
2. **Directories/Folders** (containers that hold Files or other Directories).

If you want to write a function to calculate the total size of a Directory, the code can get messy very quickly. You would have to loop through everything inside the directory, check if it's a File (and get its size) or if it's another Directory (and then loop through *that* directory... and so on). 

Handling single objects differently from grouped objects makes the code complicated and hard to maintain.

## The Solution

The Composite pattern suggests that you create a common "Interface" (or base class) for both the individual items (Files) and the containers (Directories). 

Let's call this interface a `FileSystemComponent`. It will have a method called `get_size()`.

*   For a **File**, calling `get_size()` simply returns the size of that specific file.
*   For a **Directory**, calling `get_size()` will go through everything inside it, call `get_size()` on them, add them all up, and return the total.

Now, your main program doesn't need to care whether it's looking at a single File or a massive Directory with thousands of files inside. It just calls `get_size()` and the tree structure handles the rest automatically!

## Key Components

1.  **Component**: The interface or abstract class that defines the common operations for both simple (Leaf) and complex (Composite) objects. (e.g., `FileSystemComponent` with `get_size()`).
2.  **Leaf**: The basic building block. It doesn't have any children. It does the actual work. (e.g., `File`).
3.  **Composite**: The container. It contains children (which can be Leaves or other Composites). It forwards the work to its children and combines the results. (e.g., `Directory`).
4.  **Client**: The code that interacts with the Component interface. It doesn't know if it's dealing with a Leaf or a Composite.

---

## Code Examples

### Python Implementation

Let's implement the File System analogy in Python.

```python
from abc import ABC, abstractmethod

# 1. Component: The common interface
class FileSystemComponent(ABC):
    @abstractmethod
    def get_size(self) -> int:
        pass
    
    @abstractmethod
    def display(self, indent: int = 0) -> None:
        pass

# 2. Leaf: Individual objects that don't contain anything else
class File(FileSystemComponent):
    def __init__(self, name: str, size: int):
        self.name = name
        self.size = size

    def get_size(self) -> int:
        return self.size
        
    def display(self, indent: int = 0) -> None:
        print(" " * indent + f"- File: {self.name} ({self.size} bytes)")

# 3. Composite: Containers that hold leaves or other composites
class Directory(FileSystemComponent):
    def __init__(self, name: str):
        self.name = name
        self.children = [] # Can hold Files or Directories

    def add(self, component: FileSystemComponent):
        self.children.append(component)

    def remove(self, component: FileSystemComponent):
        self.children.remove(component)

    def get_size(self) -> int:
        # It calculates its size by asking all its children for their sizes
        total_size = 0
        for child in self.children:
            total_size += child.get_size()
        return total_size
        
    def display(self, indent: int = 0) -> None:
        print(" " * indent + f"+ Directory: {self.name}")
        for child in self.children:
            child.display(indent + 4)

# 4. Client Code
if __name__ == "__main__":
    # Create files (Leaves)
    file1 = File("document.txt", 100)
    file2 = File("photo.jpg", 500)
    file3 = File("notes.md", 50)
    file4 = File("movie.mp4", 10000)

    # Create directories (Composites)
    folder1 = Directory("My Documents")
    folder2 = Directory("My Pictures")
    root_folder = Directory("Root")

    # Build the tree structure
    folder1.add(file1)
    folder1.add(file3)
    
    folder2.add(file2)
    
    root_folder.add(folder1)
    root_folder.add(folder2)
    root_folder.add(file4) # A file directly in the root

    # The client can treat directories and files exactly the same way
    print("--- File System Structure ---")
    root_folder.display()
    
    print("\n--- Sizes ---")
    print(f"Total size of 'document.txt': {file1.get_size()} bytes")
    print(f"Total size of 'My Documents' folder: {folder1.get_size()} bytes")
    print(f"Total size of 'Root' folder (Everything): {root_folder.get_size()} bytes")
```

### Java Implementation

Here is the same File System analogy written in Java.

```java
import java.util.ArrayList;
import java.util.List;

// 1. Component: The common interface
interface FileSystemComponent {
    int getSize();
    void display(int indent);
}

// 2. Leaf: Individual objects
class File implements FileSystemComponent {
    private String name;
    private int size;

    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public int getSize() {
        return this.size;
    }

    @Override
    public void display(int indent) {
        for (int i = 0; i < indent; i++) System.out.print(" ");
        System.out.println("- File: " + this.name + " (" + this.size + " bytes)");
    }
}

// 3. Composite: Containers that hold other components
class Directory implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> children = new ArrayList<>();

    public Directory(String name) {
        this.name = name;
    }

    public void addComponent(FileSystemComponent component) {
        children.add(component);
    }

    public void removeComponent(FileSystemComponent component) {
        children.remove(component);
    }

    @Override
    public int getSize() {
        int totalSize = 0;
        // The directory asks every child (could be a File or another Directory) for its size
        for (FileSystemComponent child : children) {
            totalSize += child.getSize();
        }
        return totalSize;
    }

    @Override
    public void display(int indent) {
        for (int i = 0; i < indent; i++) System.out.print(" ");
        System.out.println("+ Directory: " + this.name);
        
        for (FileSystemComponent child : children) {
            child.display(indent + 4);
        }
    }
}

// 4. Client Code
public class CompositePatternDemo {
    public static void main(String[] args) {
        // Create files (Leaves)
        File file1 = new File("document.txt", 100);
        File file2 = new File("photo.jpg", 500);
        File file3 = new File("notes.md", 50);
        File file4 = new File("movie.mp4", 10000);

        // Create directories (Composites)
        Directory folder1 = new Directory("My Documents");
        Directory folder2 = new Directory("My Pictures");
        Directory rootFolder = new Directory("Root");

        // Build the tree structure
        folder1.addComponent(file1);
        folder1.addComponent(file3);

        folder2.addComponent(file2);

        rootFolder.addComponent(folder1);
        rootFolder.addComponent(folder2);
        rootFolder.addComponent(file4); // A file directly in the root

        // Output results
        System.out.println("--- File System Structure ---");
        rootFolder.display(0);

        System.out.println("\n--- Sizes ---");
        System.out.println("Total size of 'document.txt': " + file1.getSize() + " bytes");
        System.out.println("Total size of 'My Documents' folder: " + folder1.getSize() + " bytes");
        System.out.println("Total size of 'Root' folder (Everything): " + rootFolder.getSize() + " bytes");
    }
}
```

---

## When to use the Composite Pattern?

*   **Tree-like object structures:** When you have data that can be naturally represented as a tree (like a file system, an organization chart, or a menu system with sub-menus).
*   **Uniformity:** When you want your main code (client) to be able to treat individual items and groups of items in exactly the same way without checking their specific types.

## Pros and Cons

### Pros
*   **Simplifies Client Code:** The client code is simple because it interacts with the common interface. It doesn't need to write `if/else` statements to check if it's dealing with a single item or a group.
*   **Easy to add new types:** You can easily add new types of Leaves or Composites without changing the existing client code, which follows the Open/Closed Principle.
*   **Handles complex trees naturally:** It makes it very easy to work with deeply nested structures recursively.

### Cons
*   **Overly generalized design:** By making all components implement the same interface, it might be difficult to restrict what can be added to a Composite. For example, if you wanted a special `Directory` that can *only* hold `Images`, the pattern doesn't natively enforce that (you'd have to add complex checks at runtime).
