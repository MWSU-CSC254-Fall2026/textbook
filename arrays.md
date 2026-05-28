# Arrays: 1D and 2D

This document covers the array basics you'll need for Module 1. If you've used arrays before, skim it. If not, don't worry—we'll keep it practical and come back to the tricky bits later.

## 1D Arrays: The Basics

### Declaration & Initialization

An array is a fixed-size container that holds multiple values of the same type. Think of it like a row of lockers: same size, same type, numbered starting at 0.

```java
// Declaration (just the reference, no memory yet)
int[] scores;

// Initialization (actually make the array)
scores = new int[5]; // 5 slots, all default to 0

// Or do it in one line:
String[] names = new String[3];
double[] prices = { 19.99, 5.50, 100.0 }; // size inferred
```

A few things to notice:
- The type goes *before* the brackets: `int[]`, not `[]int`.
- `new Type[size]` allocates memory on the heap (more on that in the next doc).
- If you don't explicitly set values, primitives default to `0`/`false`, and objects default to `null`.

### Iteration

You've probably seen this a million times, but here's the pattern again:

```java
// Standard for loop - when you need the index
for (int i = 0; i < scores.length; i++) {
    System.out.println("Score " + i + ": " + scores[i]);
}

// For-each loop - when you just want the values
for (int score : scores) {
    System.out.println("Score: " + score);
}
```

Quick note: `scores.length` is a *field*, not a method. No parentheses. Its not always obvious what is a field and what is a method, so a lot of the time it's something you either look up in the documentation or just experiment with. The difference between the two will become really obvious when we start making classes, but for now you can think of a field as a value (number, string, bool, etc) and a method as an action (as in, it does something with values).

### Bounds Checking

Arrays don't magically grow. If you try to access an index that doesn't exist, Java throws an `ArrayIndexOutOfBoundsException`. 

```java
int[] data = new int[3]; // valid indices: 0, 1, 2
data[3] = 100; // 💥 runtime exception
```

When using standard arrays, you should *always* (seriously, always) check if a value is in range before accessing to prevent a crash. The most common way to do this is to check the length of the array:

```java
// Lets say you have an array
// This array is of length 4, but because arrays are `zero indexed`, the value at the last position is index 3.
String[] values = {"0", "5", "6", "22"};

// And say you want to access the last item
int index = 3;

// We can check if this is valid with a check like this
// An index addressing an array should always be positive, so this first clause is a little redundant, but better safe than sorry.
// The second clause checks that the index (3 in this case) is less than (NOT <=) the length of the array (4 in this case).  
if (index >= 0 && index < values.length)
{
  System.out.println("The index is fine");
}

```

This is generally how we prevent errors when directly addressing arrays. It's a little bulky, but addressing an array improperly *will* lead to an exception. Best to save yourself the headache.

---

## 2D Arrays: Arrays of Arrays

### Syntax & Mental Model

A 2D array is just an array where each element is… another array. That's it. No magic. It's generally easy to visualize these with a matrix (a grid of numbers) and that's why you'll often see people call 2d arrays a matrix (though this is a slang term and is objectively incorrect).

```java
// Declaration + initialization
int[][] grid = new int[3][4]; // 3 rows, 4 columns

// Or with literal values:
int[][] matrix = {
    { 1, 2, 3 },
    { 4, 5, 6 },
    { 7, 8, 9 }
};
```

When addressing an item in a 2D array, Java uses *row-major* order. That's a fancy way of saying "the first index is the row, the second is the column." So `grid[1][2]` means "row 1, column 2" (which is the third element in the second row, because indexes start at 0).

Here is a quick check for understanding:

```java
// Using the same grid as above
int[][] matrix = {
    { 1, 2, 3 },
    { 4, 5, 6 },
    { 7, 8, 9 }
};

int num1 = matrix[0][2];
int num2 = matrix[2][2];
int num3 = matrix [0][0];

// If num1 is equal to 3, what do you think num2 and num3 are?
// I'll put the answer at the very end of this document.
```



### Traversal

If you are using 2D arrays, you often need to traverse or navigate them in an orderly manner. In the same way that a for loop is great at traversing a 1D array, it is also good with 2D arrays - but we need to nest them. 

```java
// Standard nested for loops
for (int row = 0; row < grid.length; row++) {
    for (int col = 0; col < grid[row].length; col++) {
        System.out.print(grid[row][col] + " ");
    }
    System.out.println(); // new line after each row
}

// For-each version (cleaner, but no index access)
for (int[] row : grid) {
    for (int value : row) {
        System.out.print(value + " ");
    }
    System.out.println();
}
```

You are welcome to trace out these loops, but both of these are saying the same general thing "for each array in this multidimensional array, loop through every item in said array". Confused? If you are, I highly recommend playing around with this code in your own environment and walking through it step by step.

There is one very strange thing about 2D arrays in Java. They can be *ragged*. That means each row can have a different length.

```java
int[][] ragged = new int[3][];
ragged[0] = new int[2]; // row 0 has 2 columns
ragged[1] = new int[5]; // row 1 has 5 columns
ragged[2] = new int[1]; // row 2 has 1 column
```

You won't use ragged arrays much in Module 1 or in general probably, but now you know they exist. 

### Common Use Cases

You most often see 2D arrays being used for a specific set of semi-related tasks.

- **Grids**: Game boards, image pixels, map coordinates
- **Matrices**: Math operations, transformations, adjacency tables
- **Tabular data**: Rows = records, columns = fields (though we'll see better ways to model this soon)

Example: a simple tic-tac-toe board
```java
String[][] board = new String[3][3];
board[0][0] = "X";
board[1][1] = "O";
// ... you get the idea
```

---

## Quick Reference Table

| Operation      | 1D Example          | 2D Example                                    |
| -------------- | ------------------- | --------------------------------------------- |
| Declare        | `int[] arr;`        | `int[][] grid;`                               |
| Initialize     | `arr = new int[5];` | `grid = new int[3][4];`                       |
| Access element | `arr[2]`            | `grid[1][3]`                                  |
| Get size       | `arr.length`        | `grid.length` (rows), `grid[0].length` (cols) |
| Iterate        | `for (int x : arr)` | nested `for` or `for-each`                    |

---

## When You'll Feel the Limits

Arrays are simple and fast, but they're rigid:
- Fixed size: can't grow or shrink after creation
- No built-in methods: want to `add`, `remove`, or `contains`? You write it yourself
- Primitive vs. object tension: `int[]` is efficient, but `ArrayList<Integer>` is more flexible (we'll bridge this gap soon)

For now, just know: arrays are your foundation. We're building on them, not throwing them away.

If any of this feels fuzzy, that's okay. The assignments will help. And if you get stuck, come back to this doc—or ask. That's what it's here for.



By the way, num2 should be 9 and num3 should be 1. If you got them right, congratulations!