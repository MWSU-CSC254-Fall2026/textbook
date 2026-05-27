# Lesson 13 - Program Architecture

### What Program Architecture Means

Program architecture describes how code is organized at a high level. It is not about individual algorithms or syntax choices. It is about how classes, packages, and responsibilities fit together to form a coherent system.

As programs grow beyond small exercises, success depends less on writing clever code and more on organizing code so that it is understandable, maintainable, and changeable. Architecture provides that structure.

Poor architecture leads to fragile systems where small changes cause widespread breakage. Good architecture allows programs to grow without becoming tangled.

---

### Packages and Modular Structure

Packages group related classes together. They are the primary unit of organization in medium and large Java programs.

A package should represent a meaningful concept or responsibility, not just a random collection of files.

Example package structure:

```text
app
├── ui
│   ├── MainMenu.java
│   └── ConsoleView.java
├── logic
│   ├── GradeCalculator.java
│   └── EnrollmentService.java
└── data
    ├── StudentRepository.java
    └── FileStorage.java
```

Each package has a clear purpose:
- *ui* handles input and output
- *logic* handles rules and computation
- *data* handles storage and retrieval

This separation makes it easier to locate code, reason about behavior, and modify one part without touching others.

---

### Layering: UI, Logic, Data

Layering is a structural pattern that separates a program into vertical slices of responsibility.

Common layers include:
- UI layer: input, output, presentation
- Logic layer: rules, decisions, calculations
- Data layer: persistence, files, databases, external sources

A key rule of layering is direction of dependency:
- UI depends on Logic
- Logic depends on Data
- Data depends on nothing above it

Example interaction flow:

```text
UI → Logic → Data
```

The UI should not directly read files or databases. The data layer should not print output or ask users for input. Each layer focuses on its job.

This separation allows changes such as replacing file storage with a database without rewriting the entire program.

---

### Separation of Concerns

Separation of concerns means that each class and package has a single, focused responsibility.

A class should answer one primary question:
“What is this class responsible for?”

Poor separation example:
- A class that reads input, validates it, calculates results, and writes output

Better separation:
- One class reads input
- One class validates data
- One class performs calculations
- One class displays results

This reduces complexity and prevents classes from becoming overly large and fragile. Smaller, focused classes are easier to test and reuse.

---

### Managing Medium-Scale Systems

Programs with 20 to 40 classes introduce new challenges:
- Remembering where logic lives
- Avoiding duplicated behavior
- Preventing dependency tangles

Strategies for managing this scale include:
- Consistent package naming
- Clear class responsibilities
- Avoiding “god classes” that do everything
- Writing small methods with descriptive names

At this scale, architecture decisions matter more than individual lines of code. Time spent organizing saves far more time later.

---

### Refactoring as an Architectural Tool

Refactoring is the process of improving code structure without changing behavior.

Common refactoring actions include:
- Splitting large classes into smaller ones
- Extracting methods with clear names
- Moving classes into more appropriate packages
- Removing duplicated logic

Refactoring is not a sign of failure. It is a normal and expected part of development. Good architecture often emerges through iterative refinement rather than upfront perfection.

---

### Reading and Structuring Unfamiliar Code

When encountering an unfamiliar codebase, architecture provides a roadmap.

Recommended approach:
- Start with the package structure
- Identify major layers or subsystems
- Locate entry points such as *main* methods or controllers
- Trace high-level flows before reading details

Avoid reading line by line at first. Understanding structure and responsibility gives context that makes detailed code easier to interpret.

---

### Domain-Driven Naming

Domain-driven naming uses terms from the problem domain rather than technical jargon.

Better names:
- Order, Invoice, Student, Enrollment

Weaker names:
- Manager1, Handler, Processor, Thing

Names should reflect real concepts the program models. This improves readability and communication, especially in team environments.

---

### Service Classes, Managers, Controllers

Service, manager, and controller classes often coordinate work between other objects.

These classes:
- Contain little or no data
- Orchestrate calls between components
- Enforce workflows or use cases

They should not replace proper object modeling. Overusing managers often indicates missing domain objects.

---

### Avoiding Cyclical Dependencies

Cyclical dependencies occur when two packages or classes depend on each other.

Example:
- Package A depends on Package B
- Package B depends on Package A

Cycles make code harder to understand and nearly impossible to reuse independently. Layered architecture and careful dependency direction help prevent this problem.

---

### Architectural Diagrams

Architectural diagrams visualize structure and relationships.

Common diagram types:
- UML package diagrams for high-level structure
- UML class diagrams for relationships and responsibilities

Diagrams are most useful when kept simple and high-level. Their purpose is communication, not exhaustive documentation.

---