# Reading: Basic File I/O (Reading & Writing CSVs in the Context of Classes)

Up until now, we have been hardcoding our data directly into our Java programs. But in the real world, data doesn't live inside your source code; it lives in databases, APIs, and files. 

Today, we are going to bridge the gap between raw data stored on a hard drive and the structured Objects we just learned to build. Specifically, we will learn how to read a **CSV (Comma-Separated Values)** file to instantiate our custom classes, and how to write our modified objects back to the hard drive.

## 1. The Anatomy of a CSV

First, let's demystify the CSV. A CSV is not a special binary format; it is just a plain text file where every line represents a row of data, and commas separate the columns. 

If you were to open `students.csv` in a basic text editor, it looks exactly like this:
```text
Alice,101,3.8
Bob,102,3.5
Charlie,103,3.9
```

To Java, this is just three Strings. It is our job to read those Strings, chop them up, and turn them into `Student` objects.

## 2. Reading from a File: `File` and `Scanner`

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

## 3. The Read Workflow: Read, Split, Parse, Instantiate

Reading a file line-by-line uses a `while` loop and the `.hasNextLine()` method. But the real magic happens inside the loop. We must follow a strict four-step pipeline for every single line:

1.  **Read:** Grab the whole line as a single String.
2.  **Split:** Chop the String into an array using `.split(",")`.
3.  **Parse:** Convert the String pieces into actual `int` and `double` primitives.
4.  **Instantiate:** Pass those primitives into our `Student` constructor.

Let's look at the complete read pipeline in action:

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
          	// The parameter taken by the split() method is what to split by
          	// You can split by anything, but we split by commas for a CSV file
          	// Because the data is separated by commas.
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
        
        System.out.println("Successfully loaded " + roster.size() + " students.");
    }
}
```

## 4. Writing Back to the File: `FileWriter` and `PrintWriter`

Reading data is only half the battle. What if we want to update a student's GPA, add a new student, or save our `ArrayList<Student>` back to the hard drive? For that, we need Output.

To write text to a file, we use two more tools:
1.  **`FileWriter`**: The low-level tool that actually connects to the file on your hard drive and writes characters to it.
2.  **`PrintWriter`**: A wrapper around `FileWriter` that gives us convenient methods like `.println()` and `.printf()`, exactly like we use with `System.out`.

```java
import java.io.FileWriter;
import java.io.PrintWriter;

public class FileSaver {
    public static void main(String[] args) throws Exception {
        // 1. Create a FileWriter pointing to our file
        // Note: By default, this OVERWRITES the existing file. 
        // If you want to append (add to the end), use: new FileWriter("students.csv", true)
        FileWriter fileWriter = new FileWriter("students.csv");
        
        // 2. Wrap it in a PrintWriter for easy printing
        PrintWriter printWriter = new PrintWriter(fileWriter);
        
        // ... we will write to the file here ...
        
        // 3. ALWAYS close your PrintWriter when done! 
        // If you forget this, your data might get stuck in memory and never actually save to the file.
        printWriter.close();
    }
}
```

## 5. The Write Workflow: Format and Save

When writing objects back to a CSV, the workflow is the exact reverse of reading. Instead of splitting and parsing, we must **format** our object's fields back into a comma-separated String and print it to the file.

Let's look at how we can take our `ArrayList<Student>` and save it back to the hard drive:

```java
import java.io.FileWriter;
import java.io.PrintWriter;
import java.util.ArrayList;

public class FileSaver {
    public static void main(String[] args) throws Exception {
        // Assume 'roster' is an ArrayList<Student> we already populated and modified
        ArrayList<Student> roster = new ArrayList<>(); 
        roster.add(new Student("Alice", 101, 3.8));
        roster.add(new Student("Bob", 102, 3.5));

        // 1. Setup the writer
        FileWriter fileWriter = new FileWriter("students_updated.csv");
        PrintWriter printWriter = new PrintWriter(fileWriter);

        // 2. Loop through our objects
        for (Student s : roster) {
            
            // 3. FORMAT the object back into a CSV line
            // We use the dot operator to get the fields, and String.format to add the commas
            String csvLine = String.format("%s,%d,%.2f", s.name, s.id, s.gpa);
          
          	// If the format method is too confusing for you, as it sometimes is for me, you can also do 
          	// this manually like so:
          	String csvLineEasy = s.name + "," + s.id + "," + s.gpa;
            
            // 4. PRINT to the file (just like System.out.println)
            printWriter.println(csvLine);
        }

        // 5. Close the writer to flush the data to the hard drive
        printWriter.close();
        
        System.out.println("Successfully saved " + roster.size() + " students to file.");
    }
}
```

## Summary

*   **CSV files** are just plain text files where commas separate data points.
*   **`File` and `Scanner`** work together to read text files line-by-line.
*   **The Read Pipeline:** To turn file data into objects, you must **Read** the line, **Split** it into an array, **Parse** the Strings into primitives, and finally **Instantiate** the object.
*   **`FileWriter` and `PrintWriter`** work together to write text to a file.
*   **The Write Pipeline:** To save objects back to a file, you must **Format** the object's fields into a comma-separated String and **Print** it using a `PrintWriter`.
*   **Bundling State:** By passing parsed data directly into a constructor, and extracting it via the dot operator, we create robust objects that eliminate the need for fragile parallel arrays.
