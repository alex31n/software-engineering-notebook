# Factory Design Pattern

The **Factory Pattern** is a widely used **Creational Design Pattern**. 

In simple terms, a Factory pattern provides a way to create objects without exposing the creation logic to the user. Instead of creating objects directly using constructors (like `new Car()`), you ask a "Factory" to create it for you. 

Think of it as delegating the responsibility of creating objects to a dedicated class.

## Real-World Analogy
Imagine you are at a **Pizza Restaurant**. 
You don't go into the kitchen, gather the dough, spread the sauce, and bake the pizza yourself. Instead, you simply tell the waiter: *"I want a Pepperoni Pizza."*

The Kitchen is the **Factory**. It takes your request ("Pepperoni"), knows exactly how to build it, and hands you the finished Pizza. You don't need to know the recipe; you just need to know what you want!

## The Problem (Why do we need it?)
If your code is filled with statements like `car = new Sedan()` or `car = new SUV()`, your main program becomes heavily dependent on those specific classes. 

If you later want to add a `Truck`, you have to go into your main code, find every place where you create vehicles, and add new `if-else` conditions. This violates the **Open-Closed Principle** (your code should be open for extension, but closed for modification).

## The Solution
We create a **Factory Class**. This class has one job: taking a simple input (like a string) and returning the correct object. 

---

## Code Example: The Shape Factory

Let's build a program that draws different shapes. Instead of the main program creating the shapes, a `ShapeFactory` will do the heavy lifting.

### Python Implementation

```python
from abc import ABC, abstractmethod

# 1. The Common Interface (The rulebook for all shapes)
class Shape(ABC):
    @abstractmethod
    def draw(self):
        pass

# 2. Concrete Classes (The actual shapes)
class Circle(Shape):
    def draw(self):
        print("Drawing a Circle 🔴")

class Square(Shape):
    def draw(self):
        print("Drawing a Square 🟦")

# 3. The Factory (The pizza kitchen)
class ShapeFactory:
    def get_shape(self, shape_type):
        if shape_type.lower() == "circle":
            return Circle()
        elif shape_type.lower() == "square":
            return Square()
        else:
            return None

# 4. Using the Factory (The Customer)
if __name__ == "__main__":
    factory = ShapeFactory()

    # We don't initialize Circle() or Square() directly!
    # We just ask the factory for them.
    shape1 = factory.get_shape("circle")
    shape1.draw()  # Output: Drawing a Circle 🔴

    shape2 = factory.get_shape("square")
    shape2.draw()  # Output: Drawing a Square 🟦
```

### Java Implementation

```java
// 1. The Common Interface
interface Shape {
    void draw();
}

// 2. Concrete Classes
class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Circle 🔴");
    }
}

class Square implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Square 🟦");
    }
}

// 3. The Factory
class ShapeFactory {
    // This method returns the Shape interface, not a specific class!
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("SQUARE")) {
            return new Square();
        }
        
        return null;
    }
}

// 4. Using the Factory
public class Main {
    public static void main(String[] args) {
        ShapeFactory factory = new ShapeFactory();

        // We don't call new Circle() or new Square() directly!
        Shape shape1 = factory.getShape("CIRCLE");
        shape1.draw();  // Output: Drawing a Circle 🔴

        Shape shape2 = factory.getShape("SQUARE");
        shape2.draw();  // Output: Drawing a Square 🟦
    }
}
```

---

## Why is this important? (Benefits)
1. **Separation of Concerns:** The code that *uses* the object doesn't need to know how to *create* the object.
2. **Easy to Update:** If we want to add a `Triangle` tomorrow, we only need to create the `Triangle` class and update the `ShapeFactory`. The main program doesn't change at all!
3. **Cleaner Code:** It hides complex creation logic. Sometimes creating an object requires fetching database records or configuring settings. The Factory hides all that ugly setup code from the rest of your app.