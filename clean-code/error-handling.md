# Error Handling

Things go wrong in software. Databases go down, network connections fail, and users provide bad input. Error handling is essential, but if it obscures the main logic of your code, it is poorly done.

Clean code handles errors gracefully without making the core logic hard to read.

## 1. Use Exceptions Instead of Error Codes

In the past, functions would return special codes (like `-1` or `99`) to indicate an error. This forced the caller to immediately check for those codes, leading to messy, deeply nested code. 

Modern languages use Exceptions. When something goes wrong, you "throw" an exception, which can be "caught" elsewhere.

### ❌ Bad Example: Returning Error Codes
```javascript
function processPayment(payment):
    variable status = chargeCreditCard(payment)
    
    if status == "SUCCESS":
        variable receiptStatus = printReceipt(payment)
        if receiptStatus == "PAPER_EMPTY":
            return "ERROR_NO_PAPER"
        return "ALL_GOOD"
    else:
        return "ERROR_CARD_DECLINED"
```

### ✅ Good Example: Throwing Exceptions
```javascript
function processPayment(payment):
    try:
        chargeCreditCard(payment)
        printReceipt(payment)
    catch CardDeclinedException:
        // handle declined card
    catch OutOfPaperException:
        // handle empty printer
```

## 2. Don't Ignore Caught Exceptions

The worst thing you can do when catching an error is nothing. Empty catch blocks hide problems, making bugs incredibly difficult to track down because the program fails silently.

### ❌ Bad Example: Empty Catch Block
```javascript
try:
    saveToDatabase(data)
catch DatabaseConnectionError:
    // TODO: fix this later
    pass // Does nothing! The error is swallowed.
```

### ✅ Good Example: Handle or Log
```javascript
try:
    saveToDatabase(data)
catch DatabaseConnectionError as error:
    logError("Failed to save data because: " + error.message)
    notifyAdmin()
```

## 3. Provide Context with Exceptions

When you throw an error, make sure it includes enough information to understand *what* failed and *why*. "An error occurred" is useless. 

### ❌ Bad Example: Vague Error
```javascript
if user.age < 18:
    throw Error("Validation failed.")
```

### ✅ Good Example: Informative Error
```javascript
if user.age < 18:
    throw Error("User validation failed: Minimum age is 18, but user is " + user.age)
```