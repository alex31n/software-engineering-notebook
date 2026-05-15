# Design Principles

This document contains the explanations on fundamental software engineering design principles. These principles serve as guidelines to help developers construct software systems that are robust, maintainable, flexible, and easy to understand. By applying these concepts, we can avoid common pitfalls and create cleaner codebases.

## Documented Principles

*   **[DRY (Don't Repeat Yourself)](./dry.md)**: A principle aimed at reducing repetition of information of all kinds, especially in software patterns. It encourages replacing repeated code with abstractions or using data normalization.
*   **[KISS (Keep It Simple, Stupid)](./kiss.md)**: A design principle stating that most systems work best if they are kept simple rather than made complex. Simplicity should be a key goal in design, and unnecessary complexity should be avoided.
*   **[YAGNI (You Aren't Gonna Need It)](./yagni.md)**: A core practice of Extreme Programming (XP) that instructs developers to not add functionality until it is absolutely necessary. It helps prevent over-engineering and wasted effort on unused features.
*   **[Composition over Inheritance](./composition-over-inheritance.md)**: An object-oriented design principle that advises developers to achieve code reuse and polymorphic behavior by assembling smaller, modular classes (composition) rather than building deep, rigid class hierarchies (inheritance).
*   **[SOLID Principles](./solid/README.md)**: An acronym representing five essential object-oriented design principles intended to make software designs more understandable, flexible, and maintainable.
    *   [Single Responsibility Principle (SRP)](./solid/1-single-responsibility-principle.md)
    *   [Open/Closed Principle (OCP)](./solid/2-open-closed-principle.md)
    *   [Liskov Substitution Principle (LSP)](./solid/3-liskov-substitution-principle.md)
    *   [Interface Segregation Principle (ISP)](./solid/4-interface-segregation-principle.md)
    *   [Dependency Inversion Principle (DIP)](./solid/5-dependency-inversion-principle.md)
