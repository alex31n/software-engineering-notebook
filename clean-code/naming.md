# Meaningful Names

One of the hardest and most important things in programming is naming things properly. We spend much more time *reading* code than writing it. If you name your variables, functions, and classes well, your code will read like a well-written book. If you name them poorly, your code will be like a confusing puzzle.

Good names act as documentation. A well-named variable explains *what* it holds and *why* it exists without needing any extra comments.

Let's look at some key rules for giving your code meaningful names, with simple pseudocode examples comparing bad (confusing) and good (clear) naming!

---

## 1. Use Intention-Revealing Names

The name of a variable, function, or class should answer the big questions: *Why does it exist? What does it do? How is it used?* If a name requires a comment to explain it, the name does not reveal its intent.

**How to fix:** Avoid single-letter variable names (except maybe for loop counters like `i` or `j`) and abbreviations that only you understand.

### ❌ Bad Naming
What is `d`? The comment tells us, but the name is useless on its own.

```javascript
// elapsed time in days
variable d = 7

function calc(a, b):
    return a * b
```

### ✅ Good Naming
Now the code is self-documenting.

```javascript
variable elapsedTimeInDays = 7

function calculateRectangularArea(width, height):
    return width * height
```

---

## 2. Use Pronounceable and Searchable Names

Programming is a social activity. You will discuss your code with teammates. If you can't pronounce a variable name out loud without sounding silly, change it. Furthermore, if you need to find where a variable is used in a massive project, a name like `MAX_CLASSES_PER_STUDENT` is much easier to search for than a name like `mcp`.

**How to fix:** Use real words. Avoid making up your own abbreviations.

### ❌ Bad Naming
Good luck talking about this in a meeting, or searching for `genymdhms` in your code!

```javascript
class DtaRcrd102:
    // generation year, month, day, hour, min, sec
    variable genymdhms = "20231025120000"
    variable modymdhms = "20231025130000"
```

### ✅ Good Naming
These names are easy to say and easy to search for.

```javascript
class CustomerRecord:
    variable generationTimestamp = "20231025120000"
    variable modificationTimestamp = "20231025130000"
```

---

## 3. Avoid Disinformation

Don't leave false clues that obscure the meaning of code. For example, don't include the word `list` in a variable name if the variable isn't actually holding a List data structure.

**How to fix:** Be precise. If it's a group of accounts, call it `account_group` or just `accounts` instead of `account_list` (unless it is strictly a List type and that distinction is critical).

### ❌ Bad Naming
The variable `hp` looks like a math variable (hypotenuse?) but it actually holds strings. The variable `accountList` is actually a dictionary/map!

```javascript
variable hp = "Harry Potter"

// This is a Map/Dictionary, not a List!
variable accountList = {"user1": "active", "user2": "inactive"} 
```

### ✅ Good Naming
Names match what the data actually represents.

```javascript
variable bookTitle = "Harry Potter"

// Plural nouns are great for collections
variable accounts = {"user1": "active", "user2": "inactive"}

// Or be specific about the structure if needed
variable accountStatusMap = {"user1": "active", "user2": "inactive"}
```

---

## 4. Class Names vs. Method Names (Nouns vs. Verbs)

There is a simple grammatical rule for naming components in Object-Oriented Programming:
- **Classes and Objects** should be **Nouns** or Noun Phrases (things).
- **Methods and Functions** should be **Verbs** or Verb Phrases (actions).

### ❌ Bad Naming
The class name is a verb, and the method name is a noun.

```javascript
class CalculateData:  // Class is an action (verb)
    variable data
    
    function finalScore():  // Method is a thing (noun)
        return sum(data)
```

### ✅ Good Naming
The class is a "Thing" (a Calculator) and the method is an "Action" (calculate).

```javascript
class ScoreCalculator:  // Class is a noun
    variable data
    
    function calculateFinalScore():  // Method is a verb
        return sum(data)
```

---

## 5. Pick One Word per Concept

It's confusing to have `fetchData`, `retrieveRecords`, and `getInformation` all doing essentially the same thing in different parts of your project. If you have a concept like "getting data from the database", pick one word for it and stick to it everywhere.

**How to fix:** Choose a standard vocabulary for your project. If you use `get`, don't use `fetch` or `retrieve` for the same type of action somewhere else.

### ❌ Bad Naming (Inconsistent)

```javascript
class UserManager:
    function fetchUser(id)

class ProductManager:
    function getProduct(id)

class OrderManager:
    function retrieveOrder(id)
```

### ✅ Good Naming (Consistent)
Now, any new developer knows exactly what method to call when they want to get an object, regardless of the class.

```javascript
class UserManager:
    function getUser(id)

class ProductManager:
    function getProduct(id)

class OrderManager:
    function getOrder(id)
```

---

## Conclusion
Good naming takes practice. Don't be afraid to rename variables as you write code; modern code editors make it very easy to rename things safely! Before you finish writing a feature, ask yourself: *"If another student or developer looks at this code in six months, will they understand what these names mean without asking me?"* If the answer is yes, you've done a great job!
