# String Manipulation

In Module 1, we used Strings constantly to print text to the console. But as we transition into Object-Oriented Programming, there is a massive paradigm shift you need to internalize: **In Java, a `String` is not a primitive data type. It is an object.** 

Because a `String` is an object, it has its own built-in methods. Mastering these methods is essential for processing data, sorting names, and parsing files. Let's look at the five most critical String methods you will use in this course.

## 1. Comparing Content: `equals()`

Because Strings are objects, variables of type `String` hold *references* (memory addresses), not the actual text. This creates a massive trap for beginners when trying to compare two Strings.

If you use the standard equality operator (`==`), Java checks if the two variables point to the *exact same location in memory*. It does **not** check if the text is the same. 

To compare the actual text content, you must use the `.equals()` method.

```java
String student1 = "Alice";
String student2 = "Alice";
String student3 = new String("Alice"); // Forces a new object in memory

// The Trap: Using ==
System.out.println(student1 == student2); // Might be true due to Java's "String Pool" optimization
System.out.println(student1 == student3); // FALSE! They are different objects in memory.

// The Solution: Using .equals()
System.out.println(student1.equals(student3)); // TRUE! The text content is identical.
```
*Rule of thumb: Never use `==` to compare Strings. Always use `.equals()`.*

## 2. Sorting Alphabetically: `compareTo()`

When we sort data (like putting a list of student names in alphabetical order), we need to know which String comes "before" the other. The `.compareTo()` method does this by comparing the two Strings lexicographically (dictionary order, based on Unicode values).

It returns an integer:
*   **`0`**: The Strings are exactly the same.
*   **A negative number** (e.g., `-1`, `-5`): The first String comes *before* the second String alphabetically.
*   **A positive number** (e.g., `1`, `12`): The first String comes *after* the second String alphabetically.

```java
String name1 = "Alice";
String name2 = "Bob";
String name3 = "alice"; // Lowercase 'a'

System.out.println(name1.compareTo(name2)); // Negative (A comes before B)
System.out.println(name2.compareTo(name1)); // Positive (B comes after A)
System.out.println(name1.compareTo(name1)); // 0 (Identical)

// Note: Uppercase letters come before lowercase letters in Unicode!
System.out.println(name1.compareTo(name3)); // Negative ('A' comes before 'a')
```
*Looking ahead: This method is the exact tool we will use when we write algorithms to sort our `ArrayList<Student>` by name!*

## 3. Extracting Parts: `substring()`

Often, you only need a specific piece of a String. The `.substring()` method allows you to extract a portion of the text. It comes in two forms:

1.  `substring(beginIndex)`: Grabs everything from the index to the end.
2.  `substring(beginIndex, endIndex)`: Grabs everything from the `beginIndex` up to, **but not including**, the `endIndex`.

*Note: Like arrays, Strings are zero-indexed.*

```java
String studentId = "ID-1024-A";

// Extract just the number part
String numberPart = studentId.substring(3, 7); // Starts at index 3, stops before 7
System.out.println(numberPart); // Outputs: 1024

// Extract everything after the last hyphen
String suffix = studentId.substring(8); 
System.out.println(suffix); // Outputs: A
```

## 4. Breaking it Apart: `split()`

If you are reading data from a file (like a CSV - Comma Separated Values file), the data usually comes in as one long String per line: `"Alice,101,3.8"`. 

The `.split()` method takes a delimiter (a character that separates data) and chops the String into an **array** of smaller Strings.

```java
String csvLine = "Alice,101,3.8";

// Split the string at every comma
String[] parts = csvLine.split(",");

System.out.println(parts[0]); // Outputs: Alice
System.out.println(parts[1]); // Outputs: 101
System.out.println(parts[2]); // Outputs: 3.8

// Because it returns an array, we can loop through it!
for (String part : parts) {
    System.out.println("Data piece: " + part);
}
```

## 5. Clean Output: `format()`

When we build our `Student` objects, we will eventually want to print them out in a clean, aligned way. Concatenating strings with `+` gets messy, especially with decimals. 

`String.format()` allows you to create a template with placeholders, and then fill them in. It uses the same syntax as `System.out.printf()`.

Common placeholders:
*   `%s` for Strings
*   `%d` for integers
*   `%f` for floating-point numbers (decimals). You can specify precision, like `%.2f` for exactly two decimal places.

```java
String name = "Alice";
int id = 101;
double gpa = 3.8;

// The old, messy way:
System.out.println("Name: " + name + " | ID: " + id + " | GPA: " + gpa);

// The clean, formatted way:
String formattedInfo = String.format("Name: %-10s | ID: %03d | GPA: %.2f", name, id, gpa);
System.out.println(formattedInfo); 
// Outputs: Name: Alice      | ID: 101 | GPA: 3.80

// Notice the tricks: 
// %-10s makes the name left-aligned in a 10-character space.
// %03d pads the ID with leading zeros if it's less than 3 digits.
// %.2f forces the GPA to show two decimal places.
```

## Summary

*   **Strings are Objects:** They have methods; treat them as such.
*   **`equals()`**: Always use this to compare text content. Never use `==`.
*   **`compareTo()`**: Returns a negative, zero, or positive integer to determine alphabetical order. Essential for sorting.
*   **`substring()`**: Extracts a specific slice of a String using start and end indexes.
*   **`split()`**: Chops a String into an array of Strings based on a delimiter. Essential for parsing files.
*   **`format()`**: Creates cleanly formatted text using placeholders like `%s`, `%d`, and `%.2f`.

*Looking ahead: We just learned how to split a String by a comma. In the next reading, we will combine this with File I/O to read actual CSV files from your hard drive, parse the data, and use it to instantiate our very first `Student` objects!*