# Clean Code

This document contains notes and guidelines on writing clean, maintainable, and readable code. Clean code is about communicating intent clearly to other developers (and your future self) and ensuring that the codebase remains robust and easy to update over time.

> "Clean code always looks like it was written by someone who cares." — Michael Feathers

## Topics Covered

Explore the following principles and practices:

*   **[Meaningful Names](./naming.md)**: The importance of choosing intention-revealing, searchable, and consistent names for variables, functions, and classes.
*   **[Functions](./functions.md)**: Guidelines for writing small, single-responsibility functions with minimal arguments.
*   **[Comments](./comments.md)**: Guidelines on when to use comments, why code should mostly be self-explanatory, and the dangers of bad or outdated comments.
*   **[Formatting](./formatting.md)**: The importance of visual structure, vertical density, the "newspaper metaphor," and enforcing team standards using automated tools.
*   **[Classes and Data Structures](./classes.md)**: Rules for keeping classes small, following the Single Responsibility Principle, and the distinction between objects and data structures.
*   **[Error Handling](./error-handling.md)**: Gracefully managing failures using exceptions instead of error codes, and providing informative context.
*   **[Boundaries](./boundaries.md)**: How to cleanly integrate and manage third-party external code without tightly coupling it to your core application logic.
*   **[Testing](./testing.md)**: Writing clean, independent, and fast automated tests using the Arrange, Act, Assert pattern.
*   **[Concurrency](./concurrency.md)**: Handling the challenges of concurrent programming by separating logic, avoiding shared data, and keeping synchronized sections small.
*   **[Refactoring](./refactoring.md)**: Techniques for restructuring existing code to improve readability and reduce complexity without changing external behavior.
*   **[Code Smells](./code-smells.md)**: Identifying and refactoring common warning signs in code like duplicated logic, magic numbers, and long methods.
*   **[Git Guideline](./git-guideline.md)**: Standardized Git practices including branching strategies, commit message formats, and security rules.
