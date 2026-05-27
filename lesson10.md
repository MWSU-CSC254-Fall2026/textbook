# Lesson 10 - Data Persistence, File I/O, and Readers

### Why Data Persistence Matters

Most programs written so far lose all their data when they stop running. As soon as the program exits, everything stored in memory disappears. Data persistence is the practice of saving information outside the program so it can be reused later.

Files are the simplest and most common persistence mechanism. They allow programs to store configuration, user input, logs, and small datasets without requiring a database or network connection.

Understanding file input and output is a foundational skill that supports later topics such as configuration-driven programs, testing with external data, and long-running applications.

---

### Files and Paths

Java represents files and file locations using abstractions rather than raw strings. This allows programs to work consistently across operating systems.

Two commonly used classes are *File* and *Path*.

- *File* is the older abstraction and represents a file or directory path.
- *Path* is newer and part of the NIO system, providing safer and more flexible path handling.

```java
File file = new File("data.txt");
```

```java
Path path = Path.of("data.txt");
```

These objects do not automatically create files. They describe where a file is expected to exist. File existence, readability, and writability must be checked explicitly.

Using these abstractions avoids hardcoding platform-specific details and makes intent clearer.

---

### Reading Files with Scanner

*Scanner* provides a simple way to read structured text input from a file. It is well suited for small files and token-based input.

```java
Scanner scanner = new Scanner(new File("data.txt"));

while (scanner.hasNextLine())
{
    String line = scanner.nextLine();
    System.out.println(line);
}

scanner.close();
```

Scanner breaks input into tokens using delimiters, which makes it convenient for reading numbers and words. However, Scanner is relatively slow and performs additional parsing that is unnecessary for large files.

Scanner is best used when readability matters more than performance.

---

### Reading Files with FileReader and BufferedReader

Lower-level readers provide more control and better performance.

*FileReader* reads raw character data from a file. On its own, it reads one character at a time, which is inefficient.

```java
FileReader reader = new FileReader("data.txt");
```

*BufferedReader* wraps another reader and reads chunks of data into memory at once.

```java
BufferedReader reader = new BufferedReader(new FileReader("data.txt"));

String line;
while ((line = reader.readLine()) != null)
{
    System.out.println(line);
}

reader.close();
```

BufferedReader is preferred for most line-based file reading. It is faster, clearer, and designed for common text-processing patterns.

---

### Writing Files with FileWriter and PrintWriter

Writing data to files mirrors the reading process.

*FileWriter* writes raw characters to a file.

```java
FileWriter writer = new FileWriter("output.txt");
writer.write("Hello\n");
writer.close();
```

*PrintWriter* adds formatting and convenience methods.

```java
PrintWriter writer = new PrintWriter(new FileWriter("output.txt"));

writer.println("First line");
writer.println("Second line");

writer.close();
```

PrintWriter simplifies writing readable text and formatted output. It is commonly used for logs, reports, and saved program state.

---

### Buffering and Why It Matters

Buffering reduces the number of expensive disk operations by grouping data in memory before reading or writing.

Without buffering:
- Each read or write may trigger a disk access.
- Performance degrades significantly with larger files.

With buffering:
- Data is transferred in chunks.
- Programs run faster and scale better.

BufferedReader and buffered writers exist specifically to solve this problem. Buffering should be the default choice unless there is a strong reason not to use it.

---

### Handling Malformed Input

Real-world files are often imperfect. Lines may be missing data, contain unexpected values, or be partially corrupted.

Programs must defensively validate input.

```java
try
{
    int value = Integer.parseInt(line);
}
catch (NumberFormatException e)
{
    System.out.println("Invalid number: " + line);
}
```

Common strategies include:
- Skipping malformed lines
- Using default values
- Reporting errors without terminating the program

Failing to handle malformed input is a common cause of crashes and unpredictable behavior.

---

### Persisting Simple Program Data

Persistence often involves saving and loading simple program state.

For example:
- High scores
- User preferences
- Small lists of records

```java
PrintWriter writer = new PrintWriter(new FileWriter("scores.txt"));
writer.println(150);
writer.println(200);
writer.close();
```

```java
BufferedReader reader = new BufferedReader(new FileReader("scores.txt"));

String line;
while ((line = reader.readLine()) != null)
{
    int score = Integer.parseInt(line);
    System.out.println(score);
}

reader.close();
```

This approach is simple, transparent, and easy to debug. Text-based persistence is often preferred early in a program’s life.

---

### Minimal Serialization with ObjectOutputStream

Java can serialize objects directly into binary form.

```java
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("data.bin"));
out.writeObject(object);
out.close();
```

Serialization requires classes to implement *Serializable* and tightly couples file format to class structure.

While powerful, serialization is fragile and version-sensitive. It should be used sparingly and with caution.

---

### CSV Read and Write Design

Comma-separated values are a common lightweight data format.

When designing CSV handling:
- Choose a clear column order
- Escape commas inside data
- Validate column counts

CSV files balance human readability with machine processing and are commonly used for imports and exports.

---

### External Data Dependencies

Programs that depend on external files must assume files may be missing, moved, or modified.

Best practices include:
- Clear error messages
- Configurable file paths
- Sensible defaults

Hard-coded paths are brittle and should be avoided.

---

### Data Corruption and Recovery Basics

Files can become corrupted due to partial writes, crashes, or manual edits.

Basic mitigation strategies include:
- Writing to a temporary file, then renaming
- Validating data before overwriting existing files
- Keeping backups of critical data

Even simple programs benefit from defensive persistence design.

---