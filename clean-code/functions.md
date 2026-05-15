# Functions

Functions (or methods) are the building blocks of any program. If your functions are poorly written, your whole application becomes difficult to understand and maintain.

Here are the golden rules for writing clean functions.

## 1. Keep them Small

Functions should be small. How small? **Smaller than that.** 
A function should ideally be just a few lines long and should be readable without having to scroll your screen.

## 2. Do One Thing (Single Responsibility)

A function should do one thing, do it well, and do it only. If a function is validating data, saving it to a database, and sending an email, it is doing three things.

### ❌ Bad Example: Doing too much
```javascript
function processUserRegistration(user):
    // 1. Validate
    if user.email is empty:
        return Error
        
    // 2. Save
    database.save(user)
    
    // 3. Notify
    sendEmail(user.email, "Welcome!")
```

### ✅ Good Example: One thing per function
```javascript
function processUserRegistration(user):
    validateUser(user)
    saveUser(user)
    sendWelcomeEmail(user)

// Each of these helper functions now does exactly ONE thing.
```

## 3. Limit the Number of Arguments

The ideal number of arguments for a function is zero (niladic). Next is one (monadic), followed closely by two (dyadic). Three arguments (triadic) should be avoided where possible. More than three requires a very special justification.

If a function needs many arguments, they can usually be grouped into an object.

### ❌ Bad Example: Too many arguments
```javascript
function createShape(x, y, width, height, color, isTransparent):
    // logic
```

### ✅ Good Example: Grouping arguments
```javascript
function createShape(shapeConfig):
    // shapeConfig contains x, y, width, height, color, etc.
```

## 4. No Flag Arguments

A "flag argument" is usually a boolean (True/False) passed into a function that tells the function to behave one way if True, and another way if False. This is a clear sign that the function is doing more than one thing!

### ❌ Bad Example: Flag argument
```javascript
function bookFlight(customer, isPremium):
    if isPremium == true:
        // book first class
    else:
        // book economy
```

### ✅ Good Example: Split into two functions
```javascript
function bookPremiumFlight(customer):
    // book first class

function bookEconomyFlight(customer):
    // book economy
```