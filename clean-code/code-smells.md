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

```javascript
function printCircleArea(radius):
    variable area = 3.14159 * radius * radius
    print("The area is " + area)

function printCylinderVolume(radius, height):
    variable baseArea = 3.14159 * radius * radius
    variable volume = baseArea * height
    print("The volume is " + volume)
```

### ✅ Clean Code
We moved the math into its own function `getCircleArea`. Now, if we want to change `3.14159` to `Math.PI`, we only have to change it in one place!

```javascript
function getCircleArea(radius):
    return 3.14159 * radius * radius

function printCircleArea(radius):
    print("The area is " + getCircleArea(radius))

function printCylinderVolume(radius, height):
    variable volume = getCircleArea(radius) * height
    print("The volume is " + volume)
```

---

## 2. Magic Numbers and Strings

This smell happens when you use numbers or text strings directly in your code without explaining what they mean. A random `86400` or `"pending"` floating in the code is hard to understand for someone reading it for the first time.

**How to fix:** Replace the magic values with named constants or variables so their meaning is obvious.

### ❌ Smelly Code
What do `1`, `2`, `0.90`, and `0.80` mean? We have to guess.

```javascript
function calculateDiscount(price, userType):
    if userType == 1:
        return price * 0.90
    else if userType == 2:
        return price * 0.80
    
    return price
```

### ✅ Clean Code
By assigning those numbers to named variables, the code reads like a plain English sentence.

```javascript
constant REGULAR_USER = 1
constant PREMIUM_USER = 2

constant REGULAR_DISCOUNT = 0.90
constant PREMIUM_DISCOUNT = 0.80

function calculateDiscount(price, userType):
    if userType == PREMIUM_USER:
        return price * PREMIUM_DISCOUNT
    else if userType == REGULAR_USER:
        return price * REGULAR_DISCOUNT
        
    return price
```

---

## 3. Long Method / Function

A function that is dozens or hundreds of lines long. It tries to do too many things at once (like reading from a database, calculating math, and sending an email all in one block).

**How to fix:** Break the long function down into smaller, well-named helper functions. Each function should do exactly one thing.

### ❌ Smelly Code
This function acts as a "God Method"—it tries to run the whole business by itself.

```javascript
function processOrder(order):
    // 1. Validate order
    if order.items.isEmpty():
        throw Error("No items")
    if order.userId == null:
        throw Error("No user")
    
    // 2. Calculate total
    variable total = 0
    for item in order.items:
        total = total + (item.price * item.quantity)
    
    // 3. Apply tax
    total = total + (total * 0.08)
    
    // 4. Save to database
    // database.save(order, total)
    
    // 5. Send email
    // emailService.send("Order processed!")
    
    return total
```

### ✅ Clean Code
The main method now reads like a table of contents. If there is a bug in the tax calculation, you know exactly which smaller function to look at.

```javascript
function processOrder(order):
    validateOrder(order)
    variable subtotal = calculateSubtotal(order.items)
    variable totalWithTax = applyTax(subtotal)
    saveOrderToDb(order, totalWithTax)
    sendConfirmationEmail(order.user)
    
    return totalWithTax
```

---

## 4. Long Parameter List

When a function takes more than 3 or 4 parameters, it becomes very hard to read. It's also very easy to accidentally pass the arguments in the wrong order (e.g., passing `city` where `country` should be).

**How to fix:** Group related parameters into a single object or class.

### ❌ Smelly Code
```javascript
function createUser(firstName, lastName, age, street, city, zipCode, country):
    // Logic to create user
```

### ✅ Clean Code
By grouping the address fields together, the method signature becomes much cleaner.

```javascript
class Address:
    variable street
    variable city
    variable zipCode
    variable country

function createUser(firstName, lastName, age, address):
    // Logic to create user
```

---

## 5. Deep Nesting (The "Arrow" Anti-Pattern)

When you have `if` statements inside `if` statements inside `for` loops, the code shifts so far to the right that it looks like an arrow `>`. This makes it incredibly hard to follow the logic.

**How to fix:** Return early (use Guard Clauses) if a condition fails, rather than wrapping the rest of the function in a giant `if` block.

### ❌ Smelly Code
```javascript
function applyDiscount(user, cart):
    if user != null:
        if user.isActive == true:
            if cart != null:
                if cart.items.isEmpty() == false:
                    // The main logic is buried deep in here!
                    return cart.total * 0.90
                    
    if cart != null:
        return cart.total
    return 0
```

### ✅ Clean Code
Check for bad conditions first and exit immediately. The "happy path" (the main logic) stays flat at the bottom.

```javascript
function applyDiscount(user, cart):
    // Guard clauses: handle invalid states and exit immediately
    if user == null or user.isActive == false:
        if cart != null: return cart.total
        return 0
        
    if cart == null or cart.items.isEmpty() == true:
        return 0
        
    // The main logic is now flat and easy to read
    return cart.total * 0.90
```

---

## Conclusion
Code smells are your code's way of asking for help. Whenever you see duplicated code, magic numbers, massive functions, too many parameters, or deep nesting, take a moment to clean it up. Writing clean code might take a few extra minutes today, but it will save you hours of headaches tomorrow!