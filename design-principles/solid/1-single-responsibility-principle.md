# Single Responsibility Principle (SRP)

## What is it?
The Single Responsibility Principle (SRP) states that **a class should have one, and only one, reason to change.**

In simpler terms: **Every class, module, or function should have exactly one job.** If your code is trying to do too many things at once, it becomes messy, hard to test, and easy to break.

## Real-World Analogy
Imagine you go to a restaurant. 
- The **Chef** only cooks the food.
- The **Waiter** only takes your order and brings you the food.
- The **Cashier** only handles your payment.

If the restaurant had only *one* person doing all three jobs, they would be overwhelmed. If the cooking process changes, that person has to learn it. If the payment system changes, that same person has to learn it.

In programming, we want our classes to be like the specialized staff members, not the overwhelmed single employee!

## The Problem (Bad Example)

Here is a `Student` class. It holds the student's data, but it also has a method to save the student's data to a text file. 

This violates SRP because it has **two reasons to change**:
1. The student's properties change (e.g., we want to add an `age` field).
2. The way we save data changes (e.g., we want to save to an SQL Database instead of a text file).

### Python
```python
class Student:
    def __init__(self, name, grade):
        self.name = name
        self.grade = grade

    def get_details(self):
        return f"{self.name} - Grade: {self.grade}"

    # ❌ VIOLATION: This class shouldn't know how to save files!
    def save_to_file(self):
        with open(f"{self.name}.txt", "w") as file:
            file.write(self.get_details())
```

### Java
```java
class Student {
    private String name;
    private String grade;

    public Student(String name, String grade) {
        this.name = name;
        this.grade = grade;
    }

    public String getDetails() {
        return name + " - Grade: " + grade;
    }

    // ❌ VIOLATION: This class shouldn't know how to save files!
    public void saveToFile() {
        try (java.io.FileWriter writer = new java.io.FileWriter(name + ".txt")) {
            writer.write(getDetails());
        } catch (java.io.IOException e) {
            e.printStackTrace();
        }
    }
}
```

## The Solution (Good Example)

To fix this, we create **two separate classes**. 
- `Student` will only manage student data.
- `StudentRepository` (or `StudentSaver`) will only handle saving data.

### Python
```python
class Student:
    def __init__(self, name, grade):
        self.name = name
        self.grade = grade

    def get_details(self):
        return f"{self.name} - Grade: {self.grade}"

# ✅ This class has only ONE job: saving Student data.
class StudentRepository:
    def save_to_file(self, student):
        with open(f"{student.name}.txt", "w") as file:
            file.write(student.get_details())
```

### Java
```java
class Student {
    private String name;
    private String grade;

    public Student(String name, String grade) {
        this.name = name;
        this.grade = grade;
    }

    public String getName() { return name; }
    
    public String getDetails() {
        return name + " - Grade: " + grade;
    }
}

// ✅ This class has only ONE job: saving Student data.
class StudentRepository {
    public void saveToFile(Student student) {
        try (java.io.FileWriter writer = new java.io.FileWriter(student.getName() + ".txt")) {
            writer.write(student.getDetails());
        } catch (java.io.IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Why is this important?
1. **Easier to Maintain:** If you decide to save data to a database later, you only change the `StudentRepository`. The `Student` class is completely unaffected!
2. **Easier to Understand:** Smaller classes with clear names tell you exactly what they do at a glance.
3. **Easier to Test:** You can test the student logic independently from the file-saving logic.
