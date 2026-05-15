# Open-Closed Principle (OCP)

## What is it?
The Open-Closed Principle says that your code (like classes, modules, or functions) should be **open for extension**, but **closed for modification**.

What does that mean?
- **Open for extension:** You should be able to add new features or behaviors to your code.
- **Closed for modification:** You should be able to add these new features *without* changing the existing, working code.

## Real-World Analogy
Imagine you have a toy robot. If you want it to have a new superpower (like shooting lasers), you shouldn't have to take the whole robot apart and change its internal wiring (modifying). Instead, you should just be able to attach a new laser module to its arm (extending).

## The Problem (Bad Example)

Here, we have an `AreaCalculator` that calculates the area for rectangles and circles. 

If we want to add a new shape, like a `Triangle`, we have to go *inside* the `AreaCalculator` class and change the calculation method (adding another `if/elif` condition). We are modifying existing code! This violates the Open-Closed Principle.

### Python
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

class Circle:
    def __init__(self, radius):
        self.radius = radius

class AreaCalculator:
    def calculate_area(self, shape):
        # ❌ VIOLATION: Checking what kind of shape it is modifies this class for every new shape.
        if isinstance(shape, Rectangle):
            return shape.width * shape.height
        elif isinstance(shape, Circle):
            return 3.14 * shape.radius * shape.radius
```

### Java
```java
class Rectangle {
    public double width;
    public double height;
}

class Circle {
    public double radius;
}

class AreaCalculator {
    public double calculateArea(Object shape) {
        // ❌ VIOLATION: Checking what kind of shape it is modifies this class for every new shape.
        if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle) shape;
            return r.width * r.height;
        } else if (shape instanceof Circle) {
            Circle c = (Circle) shape;
            return Math.PI * c.radius * c.radius;
        }
        return 0;
    }
}
```

## The Solution (Good Example)

We can fix this by using a common interface (or a base class). Let's make every shape responsible for calculating its own area.

### Python
```python
# This is our base shape. All new shapes will follow this template.
class Shape:
    def get_area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
        
    def get_area(self):
        return self.width * self.height

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
        
    def get_area(self):
        return 3.14 * self.radius * self.radius

# Now let's look at the calculator
class AreaCalculator:
    def calculate_area(self, shape):
        # ✅ We don't care what shape it is, as long as it has a get_area() method!
        return shape.get_area()

# Now we can add a Triangle without touching AreaCalculator!
class Triangle(Shape):
    def __init__(self, base, height):
        self.base = base
        self.height = height
        
    def get_area(self):
        return 0.5 * self.base * self.height
```

### Java
```java
// This is our base shape. All new shapes will follow this template.
interface Shape {
    double getArea();
}

class Rectangle implements Shape {
    private double width;
    private double height;

    public Rectangle(double width, double height) { 
        this.width = width; 
        this.height = height; 
    }
    
    public double getArea() { 
        return width * height; 
    }
}

class Circle implements Shape {
    private double radius;

    public Circle(double radius) { 
        this.radius = radius; 
    }
    
    public double getArea() { 
        return Math.PI * radius * radius; 
    }
}

// Now let's look at the calculator
class AreaCalculator {
    public double calculateArea(Shape shape) {
        // ✅ We don't care what shape it is, as long as it has a getArea() method!
        return shape.getArea();
    }
}

// Now we can add a Triangle without touching AreaCalculator!
class Triangle implements Shape {
    private double base;
    private double height;

    public Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }

    public double getArea() {
        return 0.5 * base * height;
    }
}
```

## Why is this important?
When you change existing code, you risk breaking things that are already working. By adding new features through extension (like adding new classes or using interfaces), you keep the old code safe. This makes your program much easier to update and maintain as it grows.
