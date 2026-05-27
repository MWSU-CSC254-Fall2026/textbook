# Lesson 11 - Testing and the Testing Mindset

### Why Testing Matters

Testing is not about proving that code works. It is about gaining confidence that code continues to work as it changes.

As programs grow, changes become risky. Small edits can break behavior far away from where the change was made. Testing provides a safety net that allows you to modify code, refactor designs, and fix bugs without fear of silently breaking existing functionality.

In this course, unit tests also serve a practical role. Several exercises are graded using automated unit tests. Understanding how tests work will help you interpret failures, diagnose mistakes, and design code that behaves correctly under evaluation.

---

### JUnit Basics

JUnit is the standard testing framework used in Java. It allows you to write small methods that verify specific behavior of your code.

A JUnit test class typically contains:

- Methods annotated with *@Test*
- Assertions that check expected behavior
- No *main* method

```java
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class CalculatorTest
{
    @Test
    public void add_returnsCorrectSum()
    {
        Calculator calc = new Calculator();
        int result = calc.add(2, 3);

        assertEquals(5, result);
    }
}
```

Each test method should be small and focused. If a test fails, it should be obvious what behavior is broken.

---

### Assertions

Assertions are statements that describe what must be true for the test to pass.

Common assertions include:

- *assertEquals(expected, actual)*
- *assertTrue(condition)*
- *assertFalse(condition)*
- *assertNotNull(value)*

```java
assertEquals(10, account.getBalance());
assertTrue(list.isEmpty());
assertNotNull(student);
```

Assertions form the contract between the test and the code under test. When an assertion fails, it indicates a violation of expected behavior.

---

### Writing Meaningful Tests

A meaningful test checks behavior that actually matters.

Good tests are:

- Clear in intent
- Focused on one behavior
- Independent of other tests
- Deterministic

Poor tests often:

- Test trivial getters and setters
- Depend on execution order
- Assert multiple unrelated outcomes
- Duplicate implementation logic

Test names should describe behavior, not implementation details.

```java
@Test
public void withdraw_rejectsAmountGreaterThanBalance()
{
    Account account = new Account(100);
    boolean success = account.withdraw(200);

    assertFalse(success);
}
```

The name explains what the code should do, even without reading the body.

---

### Behavior vs State Testing

State testing checks the internal state after an operation.

Behavior testing checks the observable outcome or effect.

```java
// State testing
assertEquals(50, account.getBalance());

// Behavior testing
assertFalse(account.withdraw(200));
```

Behavior-focused tests are usually more resilient. They describe what the object does, not how it stores data. This makes refactoring easier because internal representations can change without breaking tests.

---

### Making Code Testable

Code that is hard to test is often poorly designed.

Testable code tends to:

- Have small, focused methods
- Avoid hidden dependencies
- Use parameters instead of hard-coded values
- Separate logic from input and output

For example, logic that reads from files or prints to the console should be separated from computation logic. This allows tests to focus on behavior without needing complex setup.

Designing with testability in mind often improves overall code quality, even outside of testing.

---

### Regression Protection

A regression occurs when previously working behavior breaks after a change.

Tests act as regression protection by catching these breaks immediately.

When you fix a bug, adding a test that reproduces the bug ensures it never silently returns in the future. Over time, a test suite becomes a living record of what the program must continue to do.

This is especially important in coursework where changes are frequent and grading depends on consistent behavior.

---

### Test-First Development (TDD-Lite)

Test-first development writes tests before implementation.

The basic cycle is:

- Write a failing test
- Implement the simplest code to pass the test
- Refactor safely

This course does not require strict test-driven development, but understanding the mindset is valuable. Writing tests early forces you to think about interfaces, responsibilities, and edge cases before committing to an implementation.

---

### Mocking and Stubs

Mocks and stubs replace real dependencies with controlled stand-ins.

A stub returns predefined values.  
A mock verifies interactions.

These techniques are useful when code depends on databases, files, or external services. While advanced mocking is outside the core scope of this course, the concept explains why decoupled design matters.

---

### Testing Exceptions

Tests can verify that invalid input is rejected correctly.

```java
@Test
public void constructor_rejectsNegativeBalance()
{
    assertThrows(IllegalArgumentException.class, () ->
    {
        new Account(-10);
    });
}
```

Testing failure cases is just as important as testing success cases. Defensive code should behave predictably when given bad input.

---

### Coverage Basics

Coverage measures how much of the code is executed by tests.

High coverage does not guarantee good tests, and low coverage does not automatically mean bad design. Coverage is a diagnostic tool, not a goal.

In this course, coverage metrics are not required, but understanding their purpose helps contextualize professional testing practices.

---

### Black-Box vs White-Box Testing

Black-box testing treats the code as a closed system. Tests are written based on inputs and outputs only.

White-box testing considers internal structure and implementation details.

Most unit tests in this course are closer to black-box tests. They focus on observable behavior rather than private fields or internal logic. This aligns with good object-oriented design principles.

---