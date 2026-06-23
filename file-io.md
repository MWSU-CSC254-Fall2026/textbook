# Basic File I/O

Up until now, we have been hardcoding our data directly into our Java programs. But in the real world, data doesn't live inside your source code; it lives in databases, APIs, and files. 

Today, we are going to bridge the gap between raw data stored on a hard drive and the structured Objects we just learned to build. Specifically, we will learn how to read a **CSV (Comma-Separated Values)** file and use its contents to automatically instantiate our custom classes.

## 1. The Anatomy of a CSV

First, let's demystify the CSV. A CSV is not a special binary format; it is just a plain text file where every line represents a row of data, and commas separate the columns. 

If you were to open `students.csv` (a hypothetical file) in a basic text editor, it looks exactly like this:
```text
Alice,101,3.8
Bob,102,3.5
Charlie,103,3.9
```

To Java, this is just three Strings. It is our job to read those Strings, chop them up, and turn them into `Student` objects.

## 2. Opening the File: `File` and `Scanner`

To read a file, we need two tools from the Java standard library:
1.  **`File`**: Represents the actual file on your hard drive.
2.  **`Scanner`**: We've used Scanner to read keyboard input (`System.in`), but it can also read from a `File`.

```java
import java.io.File;
import java.util.Scanner;

public class FileLoader {
    public static void main(String[] args) throws Exception {
        // 1. Point to the file
        File dataFile = new File("students.csv");
        
        // 2. Create a Scanner to read it
        Scanner fileScanner = new Scanner(dataFile);
        
        // ... we will read the file here ...
        
        // 3. Always close your scanner when done!
        fileScanner.close();
    }
}
```
*Note: We add `throws Exception` to the main method signature. If Java cannot find `students.csv` in your project folder, it will throw a `FileNotFoundException`. This tells the compiler, "I know this might fail, just let it crash for now so we can focus on the I/O logic."*

## 3. The Core Workflow: Read, Split, Parse, Instantiate

Reading a file line-by-line uses a `while` loop and the `.hasNextLine()` method. But the real magic happens inside the loop. We must follow a strict four-step pipeline for every single line:

1.  **Read:** Grab the whole line as a single String.
2.  **Split:** Chop the String into an array using `.split(",")`.
3.  **Parse:** Convert the String pieces into actual `int` and `double` primitives.
4.  **Instantiate:** Pass those primitives into our `Student` constructor.

Let's look at the complete pipeline in action:

```java
import java.io.File;
import java.util.Scanner;
import java.util.ArrayList;

public class FileLoader {
    public static void main(String[] args) throws Exception {
        File dataFile = new File("students.csv");
        Scanner fileScanner = new Scanner(dataFile);
        
        // Create an ArrayList to hold our newly created objects
        ArrayList<Student> roster = new ArrayList<>();

        // Loop through the file line by line
        while (fileScanner.hasNextLine()) {
            
            // STEP 1: READ
            String currentLine = fileScanner.nextLine();
            
            // STEP 2: SPLIT
            String[] columns = currentLine.split(",");
            
            // STEP 3: PARSE (Convert Strings to primitives)
            String name = columns[0];
            int id = Integer.parseInt(columns[1]);       // String -> int
            double gpa = Double.parseDouble(columns[2]); // String -> double
            
            // STEP 4: INSTANTIATE & STORE
            Student newStudent = new Student(name, id, gpa);
            roster.add(newStudent);
        }
        
        fileScanner.close();
        
        // Prove it worked!
        System.out.println("Successfully loaded " + roster.size() + " students.");
        roster.get(0).printStudentInfo(); // Outputs: Name: Alice | ID: 101 | GPA: 3.8
    }
}
```

## 4. Why This Matters

Take a moment to look at the code above. We just read an unknown amount of data from a text file and transformed it into a fully functioning `ArrayList<Student>`. 

Because we bundled our data into a `Student` class, we didn't need three separate parallel arrays (`names[]`, `ids[]`, `gpas[]`). We didn't have to worry about index `i` in the `names` array matching index `i` in the `gpa` array. The data is intrinsically linked inside the object. 

If the CSV file has 10 lines, we get 10 `Student` objects. If it has 10,000 lines, we get 10,000 objects. The logic remains exactly the same.

## Summary

*   **CSV files** are just plain text files where commas separate data points.
*   **`File` and `Scanner`** work together to read text files line-by-line.
*   **The I/O Pipeline:** To turn file data into objects, you must **Read** the line, **Split** it into an array, **Parse** the Strings into primitives, and finally **Instantiate** the object.
*   **Bundling State:** By passing parsed data directly into a constructor, we create robust objects that eliminate the need for fragile parallel arrays.
