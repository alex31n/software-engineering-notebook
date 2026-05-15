# Formatting

Code formatting is about communication, and communication is the professional developer's first order of business. Unformatted, messy code is frustrating to read and makes the codebase look unmaintained.

## 1. The Newspaper Metaphor

Think of a well-written source file like a newspaper article:
- **The Top:** The filename and the top-level functions should give you a high-level summary of what the file does, much like a headline and opening paragraph.
- **The Middle:** As you read downward, the details increase. High-level functions call lower-level helper functions.
- **The Bottom:** The very bottom contains the lowest-level, nitty-gritty details and utility functions.

You should be able to skim the top of the file and understand the general concept without getting bogged down in details.

## 2. Vertical Density and Distance

Code that is closely related should appear close together vertically. 

- **Blank lines:** Use blank lines to separate different concepts or different steps in a process. 
- **Variable declarations:** Variables should be declared as close to their usage as possible, not all clumped at the top of a giant function.

### ❌ Bad Example: Poor vertical spacing
```javascript
function calculateTotal(price, tax):
    variable discount = 0
    variable finalPrice = 0
    // ... 50 lines of unrelated code ...
    finalPrice = price + tax - discount
    return finalPrice
```

### ✅ Good Example: Grouping related logic
```javascript
function calculateTotal(price, tax):
    // ... other logic ...
    
    variable discount = calculateDiscount()
    variable finalPrice = price + tax - discount
    return finalPrice
```

## 3. Team Rules Over Personal Preference

It doesn't matter if you personally prefer spaces over tabs, or putting curly braces on a new line vs. the same line. **What matters is that the whole team agrees on a standard and sticks to it.**

A team's codebase should look like it was written by one person, not patched together by five people with conflicting styles.

## 4. Automate Formatting

You should never waste time arguing about formatting in code reviews, and you shouldn't waste time manually pressing the spacebar to align variables.

Use automated tools!
- Choose a formatter (like Prettier for JavaScript/Web, Black for Python, gofmt for Go).
- Configure your code editor to "Format on Save".
- Use Git hooks or CI/CD pipelines to ensure poorly formatted code can never be merged into the main project.