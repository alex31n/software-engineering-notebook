# Clean Code: Refactoring

In software engineering, **Refactoring** is the process of changing the internal structure of existing code without changing its external behavior. 

Think of it like reorganizing a messy closet. You aren't buying new clothes or throwing away your favorite jacket (not changing the behavior). Instead, you are folding things, putting them into labeled boxes, and hanging up shirts so that the next time you need to find an outfit, it's quick and easy (improving the structure).

## Why do we Refactor?
- **To improve code readability:** Code is read much more often than it is written. Clean code is easier to understand.
- **To reduce complexity:** Making it easier to find and fix bugs.
- **To prepare for a new feature:** Sometimes code is too tangled to add a new feature easily. You refactor first to make room, then add the feature safely.

## When should we Refactor?
- **The Boy Scout Rule:** *"Always leave the campground cleaner than you found it."* If you open a file to add a feature and see messy code, clean it up a little bit before you leave.
- **The Rule of Three:** The first time you do something, just do it. The second time, wince at the duplication, but do it anyway. The third time you do the exact same thing, it's time to refactor!

---

Let's look at some common refactoring techniques using simple pseudocode.

## 1. Extract Function

When a function is getting too long or is doing too many different things, take a specific piece of logic and pull it out into its own well-named function.

### ❌ Before Refactoring
```javascript
function printReceipt(order):
    // Print Header
    print("************************")
    print("**** STORE RECEIPT *****")
    print("************************")
    
    // Print Details
    print("Order ID: " + order.id)
    print("Total: $" + order.total)
```

### ✅ After Refactoring
```javascript
function printReceipt(order):
    printHeader()
    printDetails(order)

function printHeader():
    print("************************")
    print("**** STORE RECEIPT *****")
    print("************************")

function printDetails(order):
    print("Order ID: " + order.id)
    print("Total: $" + order.total)
```

---

## 2. Extract Variable

Sometimes you have a long, complex expression (like a giant `if` statement) that is hard to understand. You can extract parts of that expression into well-named variables to explain what the code is doing.

### ❌ Before Refactoring
```javascript
function canBuyAlcohol(user):
    if user.age >= 21 and user.hasValidID == true and user.isIntoxicated == false:
        return true
    else:
        return false
```

### ✅ After Refactoring
```javascript
function canBuyAlcohol(user):
    variable isOldEnough = (user.age >= 21)
    variable hasID = (user.hasValidID == true)
    variable isSober = (user.isIntoxicated == false)
    
    if isOldEnough and hasID and isSober:
        return true
    else:
        return false
```

---

## 3. Decompose Conditional

Deeply nested or complex `if/else` logic can be hard to follow. You can extract the condition itself, and the blocks inside the `if` and `else`, into their own descriptive functions.

### ❌ Before Refactoring
```javascript
function calculatePrice(date, quantity, regularPrice):
    if date < SUMMER_START or date > SUMMER_END:
        return quantity * regularPrice
    else:
        return quantity * regularPrice * 0.80  // Summer discount
```

### ✅ After Refactoring
```javascript
function calculatePrice(date, quantity, regularPrice):
    if isSummer(date):
        return summerCharge(quantity, regularPrice)
    else:
        return regularCharge(quantity, regularPrice)

function isSummer(date):
    return date >= SUMMER_START and date <= SUMMER_END

function summerCharge(quantity, regularPrice):
    return quantity * regularPrice * 0.80

function regularCharge(quantity, regularPrice):
    return quantity * regularPrice
```

---

## 4. Rename Variable / Function

This is the simplest, yet most powerful refactoring technique. If a name doesn't clearly explain what a variable, function, or class does, rename it! Modern code editors allow you to safely rename variables across your entire project with a single click.

### ❌ Before Refactoring
```javascript
function doStuff(a, b):
    variable c = a * b
    return c
```

### ✅ After Refactoring
```javascript
function calculateArea(width, height):
    variable area = width * height
    return area
```

---

## The Golden Rule of Refactoring: Tests!

How do you know that changing the structure didn't accidentally break the external behavior? **Automated Tests!** 

You should ideally have tests (like Unit Tests) in place *before* you start refactoring. The safe refactoring process looks like this:
1. Run tests (they pass).
2. Change a small piece of code.
3. Run tests again (they still pass!).
4. Repeat.

If a test fails, you know exactly which small change broke it, and you can easily undo it. **Refactoring without tests is like doing a trapeze act without a safety net!**
