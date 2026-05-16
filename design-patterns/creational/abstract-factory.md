# Abstract Factory Design Pattern

The **Abstract Factory** is a creational design pattern. In simple terms, it is a "factory of factories." 

It allows you to create **families of related objects** without specifying their exact classes. The main goal is to make sure that the objects you create are compatible with each other.

## Real-World Analogy
Imagine you are building user interfaces (UI) and your app supports both **Windows** and **macOS** designs. 

Your UI needs a **Button** and a **Checkbox**. 
* If the user is on Windows, you want a Windows-style Button and a Windows-style Checkbox. 
* If the user is on a Mac, you want a Mac-style Button and a Mac-style Checkbox. 

You **never** want to accidentally mix a Mac-style Button with a Windows-style Checkbox! 

The Abstract Factory solves this by giving you a specific factory (like a `WindowsUIFactory` or `MacUIFactory`). Once you choose your factory, you are guaranteed that every button and checkbox it produces will perfectly match the operating system.

## The Core Parts
1. **Abstract Products**: The interfaces for a set of distinct but related products (e.g., `Button`, `Checkbox`).
2. **Concrete Products**: Different implementations of the abstract products, grouped by variants (e.g., `MacButton`, `WindowsButton`).
3. **Abstract Factory**: The interface that declares creation methods for each abstract product.
4. **Concrete Factories**: The classes that implement the Abstract Factory. Each concrete factory creates a specific variant of products (e.g., `MacFactory` only creates Mac products).

---

## Example 1: Python Implementation

In Python, we use the `abc` (Abstract Base Classes) module to create our interfaces.

```python
from abc import ABC, abstractmethod

# -----------------------------------------
# 1. Abstract Products
# -----------------------------------------
class Button(ABC):
    @abstractmethod
    def paint(self):
        pass

class Checkbox(ABC):
    @abstractmethod
    def paint(self):
        pass

# -----------------------------------------
# 2. Concrete Products
# -----------------------------------------
# Mac Family
class MacButton(Button):
    def paint(self):
        print("Rendering a Mac-style Button.")

class MacCheckbox(Checkbox):
    def paint(self):
        print("Rendering a Mac-style Checkbox.")

# Windows Family
class WindowsButton(Button):
    def paint(self):
        print("Rendering a Windows-style Button.")

class WindowsCheckbox(Checkbox):
    def paint(self):
        print("Rendering a Windows-style Checkbox.")

# -----------------------------------------
# 3. Abstract Factory
# -----------------------------------------
class GUIFactory(ABC):
    @abstractmethod
    def create_button(self) -> Button:
        pass

    @abstractmethod
    def create_checkbox(self) -> Checkbox:
        pass

# -----------------------------------------
# 4. Concrete Factories
# -----------------------------------------
class MacFactory(GUIFactory):
    def create_button(self) -> Button:
        return MacButton()

    def create_checkbox(self) -> Checkbox:
        return MacCheckbox()

class WindowsFactory(GUIFactory):
    def create_button(self) -> Button:
        return WindowsButton()

    def create_checkbox(self) -> Checkbox:
        return WindowsCheckbox()

# -----------------------------------------
# 5. Client Code (Putting it all together)
# -----------------------------------------
def render_application_ui(factory: GUIFactory):
    """
    The client code doesn't know (or care) which exact factory it gets.
    It just knows that the factory can create matching buttons and checkboxes!
    """
    button = factory.create_button()
    checkbox = factory.create_checkbox()
    
    button.paint()
    checkbox.paint()

# Simulating the app choosing a factory based on the OS
print("--- Running on Mac OS ---")
render_application_ui(MacFactory())

print("\n--- Running on Windows OS ---")
render_application_ui(WindowsFactory())
```

---

## Example 2: Java Implementation

In Java, we use `interface` to define our abstract products and factories.

```java
// -----------------------------------------
// 1. Abstract Products
// -----------------------------------------
interface Button {
    void paint();
}

interface Checkbox {
    void paint();
}

// -----------------------------------------
// 2. Concrete Products
// -----------------------------------------
class MacButton implements Button {
    public void paint() { System.out.println("Rendering a Mac-style Button."); }
}
class MacCheckbox implements Checkbox {
    public void paint() { System.out.println("Rendering a Mac-style Checkbox."); }
}

class WindowsButton implements Button {
    public void paint() { System.out.println("Rendering a Windows-style Button."); }
}
class WindowsCheckbox implements Checkbox {
    public void paint() { System.out.println("Rendering a Windows-style Checkbox."); }
}

// -----------------------------------------
// 3. Abstract Factory
// -----------------------------------------
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// -----------------------------------------
// 4. Concrete Factories
// -----------------------------------------
class MacFactory implements GUIFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

class WindowsFactory implements GUIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

// -----------------------------------------
// 5. Client Code
// -----------------------------------------
public class AbstractFactoryExample {
    
    public static void renderApplicationUI(GUIFactory factory) {
        Button button = factory.createButton();
        Checkbox checkbox = factory.createCheckbox();
        
        button.paint();
        checkbox.paint();
    }

    public static void main(String[] args) {
        System.out.println("--- Running on Mac OS ---");
        renderApplicationUI(new MacFactory());
        
        System.out.println("\n--- Running on Windows OS ---");
        renderApplicationUI(new WindowsFactory());
    }
}
```

## Why should I use this?
* **Consistency:** It guarantees that the products you extract from a factory are compatible with each other.
* **Flexibility:** You can introduce new variants of products (like a `LinuxFactory` for Linux UI) without breaking any of the existing client code.
* **Clean Code:** It strictly adheres to the **Single Responsibility Principle** (creation code is extracted into one place) and the **Open/Closed Principle** (you can introduce new factories without changing existing code).