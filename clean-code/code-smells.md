# Code Smells

Imagine you walk into your kitchen and notice a bad smell. The smell itself isn't the main problem; rather, it's a warning sign that there might be rotten food in the fridge or trash that needs to be taken out.

In programming, a **Code Smell** is exactly that. It's usually not a bug—the code probably works correctly and passes tests—but it's a hint that there might be a deeper problem in how the code is designed or written. Code smells make your code harder to read, harder to maintain, and much easier to break in the future when you try to add new features.

Let's look at some of the most common code smells, how to spot them, and how to "refactor" (clean) them!

---

## 1. Duplicated Code (The "Copy-Paste" Smell)

This is the most common smell. You find the exact same (or very similar) lines of code in multiple places. If you need to change the logic later, you have to remember to change it everywhere. If you forget one place, you just created a bug!

**How to fix:** Extract the duplicated logic into a single reusable function or method.

### ❌ Smelly Code
Notice how the math for the circle's area is written twice.

**Python:**
```python
def print_circle_area(radius):
    area = 3.14159 * radius * radius
    print(f"The area is {area}")

def print_cylinder_volume(radius, height):
    base_area = 3.14159 * radius * radius
    volume = base_area * height
    print(f"The volume is {volume}")
```

**Java:**
```java
public void printCircleArea(double radius) {
    double area = 3.14159 * radius * radius;
    System.out.println("The area is " + area);
}

public void printCylinderVolume(double radius, double height) {
    double baseArea = 3.14159 * radius * radius;
    double volume = baseArea * height;
    System.out.println("The volume is " + volume);
}
```

### ✅ Clean Code
We moved the math into its own function `get_circle_area` / `getCircleArea`. Now, if we want to change `3.14159` to `Math.PI`, we only have to change it in one place!

**Python:**
```python
def get_circle_area(radius):
    return 3.14159 * radius * radius

def print_circle_area(radius):
    print(f"The area is {get_circle_area(radius)}")

def print_cylinder_volume(radius, height):
    volume = get_circle_area(radius) * height
    print(f"The volume is {volume}")
```

**Java:**
```java
public double getCircleArea(double radius) {
    return 3.14159 * radius * radius;
}

public void printCircleArea(double radius) {
    System.out.println("The area is " + getCircleArea(radius));
}

public void printCylinderVolume(double radius, double height) {
    double volume = getCircleArea(radius) * height;
    System.out.println("The volume is " + volume);
}
```

---

## 2. Magic Numbers and Strings

This smell happens when you use numbers or text strings directly in your code without explaining what they mean. A random `86400` or `"pending"` floating in the code is hard to understand for someone reading it for the first time.

**How to fix:** Replace the magic values with named constants or variables so their meaning is obvious.

### ❌ Smelly Code
What do `1`, `2`, `0.90`, and `0.80` mean? We have to guess.

**Python:**
```python
def calculate_discount(price, user_type):
    if user_type == 1:
        return price * 0.90
    elif user_type == 2:
        return price * 0.80
    return price
```

**Java:**
```java
public double calculateDiscount(double price, int userType) {
    if (userType == 1) {
        return price * 0.90;
    } else if (userType == 2) {
        return price * 0.80;
    }
    return price;
}
```

### ✅ Clean Code
By assigning those numbers to named variables, the code reads like a plain English sentence.

**Python:**
```python
REGULAR_USER = 1
PREMIUM_USER = 2

REGULAR_DISCOUNT = 0.90
PREMIUM_DISCOUNT = 0.80

def calculate_discount(price, user_type):
    if user_type == PREMIUM_USER:
        return price * PREMIUM_DISCOUNT
    elif user_type == REGULAR_USER:
        return price * REGULAR_DISCOUNT
    return price
```

**Java:**
```java
public static final int REGULAR_USER = 1;
public static final int PREMIUM_USER = 2;

public static final double REGULAR_DISCOUNT = 0.90;
public static final double PREMIUM_DISCOUNT = 0.80;

public double calculateDiscount(double price, int userType) {
    if (userType == PREMIUM_USER) {
        return price * PREMIUM_DISCOUNT;
    } else if (userType == REGULAR_USER) {
        return price * REGULAR_DISCOUNT;
    }
    return price;
}
```

---

## 3. Long Method / Function

A function that is dozens or hundreds of lines long. It tries to do too many things at once (like reading from a database, calculating math, and sending an email all in one block).

**How to fix:** Break the long function down into smaller, well-named helper functions. Each function should do exactly one thing.

### ❌ Smelly Code
This function acts as a "God Method"—it tries to run the whole business by itself.

**Python (Pseudo-code):**
```python
def process_order(order):
    # 1. Validate order
    if not order.items:
        raise Exception("No items")
    if not order.user_id:
        raise Exception("No user")
    
    # 2. Calculate total
    total = 0
    for item in order.items:
        total += item.price * item.quantity
    
    # 3. Apply tax
    total = total + (total * 0.08)
    
    # 4. Save to database
    # database.save(order, total)
    
    # 5. Send email
    # email_service.send("Order processed!")
    return total
```

**Java (Pseudo-code):**
```java
public double processOrder(Order order) throws Exception {
    // 1. Validate order
    if (order.getItems().isEmpty()) {
        throw new Exception("No items");
    }
    if (order.getUserId() == null) {
        throw new Exception("No user");
    }
    
    // 2. Calculate total
    double total = 0;
    for (Item item : order.getItems()) {
        total += item.getPrice() * item.getQuantity();
    }
    
    // 3. Apply tax
    total = total + (total * 0.08);
    
    // 4. Save to database
    // database.save(order, total);
    
    // 5. Send email
    // emailService.send("Order processed!");
    
    return total;
}
```

### ✅ Clean Code
The main method now reads like a table of contents. If there is a bug in the tax calculation, you know exactly which smaller function to look at.

**Python:**
```python
def process_order(order):
    validate_order(order)
    subtotal = calculate_subtotal(order.items)
    total_with_tax = apply_tax(subtotal)
    save_order_to_db(order, total_with_tax)
    send_confirmation_email(order.user)
    return total_with_tax
```

**Java:**
```java
public double processOrder(Order order) throws Exception {
    validateOrder(order);
    double subtotal = calculateSubtotal(order.getItems());
    double totalWithTax = applyTax(subtotal);
    saveOrderToDb(order, totalWithTax);
    sendConfirmationEmail(order.getUser());
    return totalWithTax;
}
```

---

## 4. Long Parameter List

When a function takes more than 3 or 4 parameters, it becomes very hard to read. It's also very easy to accidentally pass the arguments in the wrong order (e.g., passing `city` where `country` should be).

**How to fix:** Group related parameters into a single object or class.

### ❌ Smelly Code
**Python:**
```python
def create_user(first_name, last_name, age, street, city, zip_code, country):
    # Logic to create user
    pass
```

**Java:**
```java
public void createUser(String firstName, String lastName, int age, String street, String city, String zipCode, String country) {
    // Logic to create user
}
```

### ✅ Clean Code
By grouping the address fields together, the method signature becomes much cleaner.

**Python:**
```python
class Address:
    def __init__(self, street, city, zip_code, country):
        self.street = street
        self.city = city
        self.zip_code = zip_code
        self.country = country

def create_user(first_name, last_name, age, address):
    # Logic to create user
    pass
```

**Java:**
```java
class Address {
    String street;
    String city;
    String zipCode;
    String country;
    // Constructor and getters omitted for brevity
}

public void createUser(String firstName, String lastName, int age, Address address) {
    // Logic to create user
}
```

---

## 5. Deep Nesting (The "Arrow" Anti-Pattern)

When you have `if` statements inside `if` statements inside `for` loops, the code shifts so far to the right that it looks like an arrow `>`. This makes it incredibly hard to follow the logic.

**How to fix:** Return early (use Guard Clauses) if a condition fails, rather than wrapping the rest of the function in a giant `if` block.

### ❌ Smelly Code
**Python:**
```python
def apply_discount(user, cart):
    if user is not None:
        if user.is_active:
            if cart is not None:
                if len(cart.items) > 0:
                    # The main logic is buried deep in here!
                    return cart.total * 0.90
    return cart.total if cart else 0
```

**Java:**
```java
public double applyDiscount(User user, Cart cart) {
    if (user != null) {
        if (user.isActive()) {
            if (cart != null) {
                if (!cart.getItems().isEmpty()) {
                    // The main logic is buried deep in here!
                    return cart.getTotal() * 0.90;
                }
            }
        }
    }
    return cart != null ? cart.getTotal() : 0;
}
```

### ✅ Clean Code
Check for bad conditions first and exit immediately. The "happy path" (the main logic) stays flat at the bottom.

**Python:**
```python
def apply_discount(user, cart):
    # Guard clauses: handle invalid states and exit immediately
    if user is None or not user.is_active:
        return cart.total if cart else 0
        
    if cart is None or len(cart.items) == 0:
        return 0
        
    # The main logic is now flat and easy to read
    return cart.total * 0.90
```

**Java:**
```java
public double applyDiscount(User user, Cart cart) {
    // Guard clauses: handle invalid states and exit immediately
    if (user == null || !user.isActive()) {
        return cart != null ? cart.getTotal() : 0;
    }
    
    if (cart == null || cart.getItems().isEmpty()) {
        return 0;
    }
    
    // The main logic is now flat and easy to read
    return cart.getTotal() * 0.90;
}
```

---

## Conclusion
Code smells are your code's way of asking for help. Whenever you see duplicated code, magic numbers, massive functions, too many parameters, or deep nesting, take a moment to clean it up. Writing clean code might take a few extra minutes today, but it will save you hours of headaches tomorrow!
