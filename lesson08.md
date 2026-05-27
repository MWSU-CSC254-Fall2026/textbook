# Lesson 8 - Exception Handling and Error Design

### Why Exceptions Exist

Programs fail for many reasons. Files are missing, input is invalid, network connections drop, and assumptions you made earlier turn out to be false. Java’s exception system exists to give you a structured way to represent failure, propagate it through a program, and respond appropriately.

Exceptions are not about crashing programs. They are about making failure explicit, controlled, and understandable. When used well, exceptions document assumptions, enforce contracts, and prevent corrupted program state. When used poorly, they hide bugs and make debugging far harder.

---

### Checked vs Unchecked Exceptions

Java divides exceptions into two broad categories based on how the compiler treats them.

Checked exceptions are exceptions that the compiler requires you to handle or declare. These usually represent external conditions you cannot fully control, such as file access or network operations.

Unchecked exceptions are subclasses of RuntimeException. These represent programming errors, violated assumptions, or invalid internal state.

```java
public void readFile(String path) throws IOException
{
    Files.readAllLines(Path.of(path));
}
```

This method must declare IOException because it is a checked exception. Any code that calls this method must either handle the exception or propagate it.

```java
public int divide(int a, int b)
{
    return a / b;
}
```

Division by zero throws an unchecked exception. The compiler does not force you to handle it because it represents a logic error rather than a recoverable condition.

Checked exceptions push error handling decisions upward. Unchecked exceptions signal that something has gone fundamentally wrong and execution should usually stop.

---

### try, catch, and finally

The try-catch structure allows you to detect and respond to exceptions at runtime.

```java
try
{
    int result = divide(10, 0);
}
catch (ArithmeticException e)
{
    System.out.println("Cannot divide by zero");
}
finally
{
    System.out.println("Cleanup happens here");
}
```

Code inside the try block is monitored for exceptions. If an exception occurs, execution jumps to the first compatible catch block. The finally block always runs, whether an exception occurred or not.

Finally is commonly used for cleanup tasks such as closing files or releasing locks. Even if an exception is thrown or returned early, finally still executes.

---

### try-with-resources

Many objects represent resources that must be closed, such as files, scanners, and streams. Java provides try-with-resources to guarantee cleanup without requiring a finally block.

```java
try (Scanner scanner = new Scanner(new File("data.txt")))
{
    while (scanner.hasNextLine())
    {
        System.out.println(scanner.nextLine());
    }
}
```

Any object that implements AutoCloseable can be used in this construct. When the block exits, the resource is automatically closed, even if an exception occurs.

This pattern prevents resource leaks and should always be preferred over manual close calls.

---

### Custom Exceptions

Sometimes built-in exceptions are not expressive enough. Custom exceptions allow you to name specific failure conditions in your domain.

```java
public class InvalidGradeException extends Exception
{
    public InvalidGradeException(String message)
    {
        super(message);
    }
}
```

You can then use the exception to enforce rules clearly.

```java
public void addGrade(int grade) throws InvalidGradeException
{
    if (grade < 0 || grade > 100)
    {
        throw new InvalidGradeException("Grade must be between 0 and 100");
    }
}
```

Custom exceptions improve readability and make error handling more precise. They also serve as documentation for method contracts.

---

### Designing Error Messages

An exception message should explain what went wrong and why it matters, not how the code failed internally.

Poor messages:
- "Error occurred"
- "Invalid input"

Better messages:
- "Grade must be between 0 and 100"
- "File config.txt not found in application directory"

Messages should be written for developers first, users second. Avoid leaking implementation details unless the exception is intended for debugging only.

Clear messages reduce debugging time dramatically.

---

### Anti-Patterns to Avoid

One of the most common mistakes with exceptions is swallowing them.

```java
try
{
    readFile("data.txt");
}
catch (IOException e)
{
}
```

This code hides failure and makes debugging nearly impossible. If an exception is caught, it must be handled meaningfully or rethrown.

Blanket catches are also dangerous.

```java
catch (Exception e)
{
    System.out.println("Something went wrong");
}
```

Catching overly broad exceptions often masks bugs and prevents appropriate responses.

Empty catches, blanket catches, and ignored exceptions all violate the purpose of exceptions. Every catch block should have a clear reason for existing.

---

### Fail-Fast vs Fail-Safe Design

Fail-fast design throws exceptions as soon as invalid state is detected. This prevents errors from spreading and corrupting data.

Fail-safe design attempts to continue execution by substituting defaults or partial behavior.

Fail-fast is generally preferred for logic errors and violated assumptions. Fail-safe can be appropriate for user input or non-critical operations where recovery is reasonable.

The key is to decide intentionally, not accidentally.

---

### Exceptions as Contract Enforcement

Methods often rely on assumptions about their inputs. Exceptions provide a way to enforce those assumptions.

```java
public void setAge(int age)
{
    if (age < 0)
    {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

This approach makes invalid usage immediately visible. It prevents silent corruption and shifts responsibility to the caller.

Exceptions used this way clarify the boundary between valid and invalid behavior.

---

### Exception Chaining

Sometimes an exception occurs because of another exception. Exception chaining preserves the original cause.

```java
try
{
    readFile("config.txt");
}
catch (IOException e)
{
    throw new RuntimeException("Failed to load configuration", e);
}
```

The original exception is preserved as the cause. This makes debugging far easier because the full chain of failure is visible.

Never discard useful context when rethrowing exceptions.

---

### Logging vs Printing Stack Traces

Printing a stack trace directly to standard output is common when learning Java or working on very small programs.

```java
e.printStackTrace();
```

This approach is simple and immediate. You see exactly where an exception occurred and how execution reached that point. For debugging homework, quick experiments, or short-lived programs, this is often sufficient.

However, printing stack traces has serious limitations in real systems. Output goes to the console, which may not be visible, may be mixed with unrelated messages, or may disappear entirely once a program is deployed. You also have no control over verbosity, formatting, or where the information is stored.

Logging frameworks solve these problems by treating error information as structured data rather than raw output. Logging allows you to:

- Control severity levels such as debug, info, warning, and error
- Redirect output to files, remote servers, or monitoring systems
- Include timestamps, thread information, and contextual metadata
- Enable or disable logging without changing program logic

Most importantly, logging separates failure reporting from failure handling. Your code can respond to an error appropriately while still recording diagnostic information for later analysis.

As programs grow beyond simple console applications, stack trace printing becomes noise rather than insight. Logging becomes the primary tool for understanding failures that occur in production, where direct debugging is not possible.

For this reason, printing stack traces should be treated as a temporary learning tool, while logging should be viewed as part of long-term program design.

---

### Assertions vs Exceptions

Assertions are used to verify assumptions during development.

```java
assert value >= 0;
```

Assertions can be disabled at runtime and should never be used for input validation or user-facing error handling.

Exceptions are part of program behavior. Assertions are development-time checks.

Use assertions to catch programmer mistakes early. Use exceptions to handle real runtime failures. 

For the record, assertions are what I use for the autograding tests you'll find in your programming assignments.

---

### Summary

Exception handling is not about avoiding crashes. It is about designing failure paths as carefully as success paths. Clear exception types, meaningful messages, and intentional handling decisions lead to programs that are easier to reason about and safer to extend.

Poor exception design hides errors and creates fragile systems. Good exception design communicates intent, enforces contracts, and keeps failures visible and manageable.

This lesson connects directly to API design, testing strategies, and defensive programming techniques you will encounter later.