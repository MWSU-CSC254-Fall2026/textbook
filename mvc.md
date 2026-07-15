# Separation of Concerns: MVC

If you just finished building your first procedural GUI, your code probably feels a bit chaotic. You likely have a massive `main` method or a single giant class where UI components are declared right next to the mathematical logic or data processing. 

Worse, your event handling is probably trapped in a massive `if-else` chain inside a single `actionPerformed` method. Every time a user clicks a button, the program has to evaluate a long list of conditions: *Was it the calculate button? Was it the clear button? Was it the exit button?* 

This approach works for a tiny, trivial application, but it quickly becomes unmaintainable. This is a classic example of **high coupling**. Your business logic is permanently glued to your visual components. If you want to add a new feature, you have to dig through a tangled web of UI code. If you want to change the underlying math, you risk breaking the visual layout. 

Furthermore, this makes testing nearly impossible. In professional software development, we write automated unit tests for our business logic. But how do you write a unit test for a calculation method if that method requires a `JFrame` and a `JButton` to exist in memory just to run? You can't. 

It is time to refactor. It is time to separate our concerns.

## The Antidote: The Model-View-Controller Pattern

To fix this, we borrow a fundamental architectural concept from software engineering: the **Model-View-Controller (MVC)** pattern. MVC forces us to split our application into three distinct, specialized classes, each with a single, strict responsibility.

Think of a restaurant. The **Model** is the kitchen (where the raw ingredients and recipes live). The **View** is the dining room and the menu (what the customer sees). The **Controller** is the waiter (who takes the customer's order from the dining room, gives it to the kitchen, and brings the finished food back to the customer).

*   **The Model:** This is the brain of your application. It holds the data and contains all the business logic. The Model knows absolutely nothing about buttons, text fields, or screens. It just processes data and manages state. Because it has no UI dependencies, it is incredibly easy to test.
*   **The View:** This is the face of your application. It contains all the Swing components. The View knows how to draw the UI and capture user input, but it contains zero business logic. It doesn't know how to calculate anything; it just displays what it is told and passes raw user input along.
*   **The Controller:** This is the nervous system. It acts as the bridge between the Model and the View. When the View captures a user click, it passes that event to the Controller. The Controller interprets the action, tells the Model to update its data, and finally tells the View to refresh its display with the Model's new data.

## A Concrete MVC Example

To see how these pieces fit together, let's look at a simplified example of a "Number Doubler" application. Notice how the classes only know about the specific things they need to do their jobs.

First, the **Model**. It just holds a number and knows how to double it.

```java
public class NumberModel {
    private int currentValue = 0;

    public void doubleValue() {
        currentValue = currentValue * 2;
    }

    public int getValue() {
        return currentValue;
    }
    
    public void reset() {
        currentValue = 0;
    }
}
```

Next, the **View**. It just builds the screen and provides getters so the Controller can interact with the components. It does no math.

```java
import javax.swing.*;

public class NumberView {
    private JFrame frame;
    private JLabel valueLabel;
    private JButton doubleButton;
    private JButton resetButton;

    public NumberView() {
        frame = new JFrame("Number Doubler");
        valueLabel = new JLabel("Current Value: 0");
        doubleButton = new JButton("Double It");
        resetButton = new JButton("Reset");
        
        // Layout and frame setup omitted for brevity...
    }

    // Getters allow the Controller to access the UI components
    public JButton getDoubleButton() { return doubleButton; }
    public JButton getResetButton() { return resetButton; }
    
    public void updateDisplay(int newValue) {
        valueLabel.setText("Current Value: " + newValue);
    }
}
```

Finally, the **Controller**. It takes the Model and the View, and wires them together.

```java
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class NumberController {
    private NumberModel model;
    private NumberView view;

    public NumberController(NumberModel model, NumberView view) {
        this.model = model;
        this.view = view;
        
        wireListeners();
    }

    private void wireListeners() {
        // Wiring logic goes here (explained in the next section)
    }
}
```

Notice the beauty of this structure? If we want to change the application to *triple* the number instead of doubling it, we only edit `NumberModel.java`. The View and Controller remain completely untouched. 

## Ditching the If-Else Chain: Event Delegation

Now that we have separated our classes, we need a better way to handle button clicks than a giant `if-else` block in the Controller. We do this by embracing the **Event Delegation Model**.

In this model, the lifecycle of an interaction is strictly defined and delegated to specialized objects:
1.  **Source:** A UI component (like a `JButton`) generates an event when interacted with.
2.  **Event Object:** The source creates an Event Object (like an `ActionEvent`) that contains details about what just happened.
3.  **Listener:** The source delegates the handling of this event to a registered Listener object.

Instead of having one giant listener that figures out what to do using `if-else` statements, we register specific, focused listeners directly to specific components. The `JButton` doesn't care *how* the event is handled, it just passes the `ActionEvent` to whoever is listening. This is a powerful application of polymorphism.

## Anonymous Inner Classes for Clean Listeners

To implement these focused listeners without creating dozens of separate, named Java files, we use **Anonymous Inner Classes**. 

Let's break down the term:
*   **Inner Class:** A class defined inside another class. Our listeners need to be inside the Controller because they need access to the Controller's `model` and `view` variables.
*   **Anonymous:** The class has no name. We don't need to reuse it, so giving it a name like `CalculateButtonListener` is just unnecessary clutter.

An anonymous inner class allows you to declare and instantiate a class at the exact same time, right inside the method where you need it. Here is how you replace the `if-else` chain with anonymous listeners in your Controller:

```java
private void wireListeners() {
    
    // The double button gets its own dedicated, anonymous listener
    view.getDoubleButton().addActionListener(new ActionListener() {
        @Override
        public void actionPerformed(ActionEvent e) {
            // 1. Tell the Model to process the logic
            model.doubleValue();
            
            // 2. Tell the View to update with the Model's new data
            view.updateDisplay(model.getValue());
        }
    });

    // The reset button gets its own separate, anonymous listener
    view.getResetButton().addActionListener(new ActionListener() {
        @Override
        public void actionPerformed(ActionEvent e) {
            model.reset();
            view.updateDisplay(model.getValue());
        }
    });
}
```

Notice how clean this is? No `if-else` chains. No checking `e.getSource()`. Each button has its own isolated block of code that knows exactly what it is supposed to do. If you need to add a third button, you just add a third anonymous listener. You don't risk breaking the existing logic.

## Organizing the View: Hierarchy and Layouts

With the logic moved to the Model and Controller, your View class is now free to focus entirely on visual organization. To do this effectively, you need to understand the **GUI Component Hierarchy** and **Layout Managers**.

Everything in Swing is a `Component`, but some components are also `Containers`. A Container is simply a Component that can hold other Components inside it.

*   **The Frame:** The `JFrame` is your top-level window. It is a Container.
*   **The Panels:** `JPanel` is the most common intermediate Container. You use Panels to group related components together. 
*   **The Components:** `JLabel`, `JTextField`, and `JButton` are the leaf nodes. They cannot hold other components.

When you add components to a Container, you need to tell the Container how to arrange them. This is the job of a **Layout Manager**. 

The secret to complex GUIs is not finding a "complex" layout manager. The secret is **nesting**. You create complex layouts by putting simple panels inside other panels, each with its own simple layout manager. 

For your assignments, you will primarily use these layout managers:

*   **FlowLayout:** The default for `JPanel`. It simply arranges components in a row, left-to-right, wrapping to the next line if it runs out of space. It is perfect for simple rows of buttons or input fields.
*   **BorderLayout:** The default for `JFrame`. It divides the container into five regions: `NORTH`, `SOUTH`, `EAST`, `WEST`, and `CENTER`. It is perfect for placing a title at the top, a status bar at the bottom, and your main data view in the middle. Note that the `CENTER` region is greedy; it will expand to fill all leftover space.
*   **GridLayout:** Arranges components in a strict grid of rows and columns. Great for calculator keypads.

Here is an example of how you use nested panels to build a structured View:

```java
// Inside your View class constructor
JFrame frame = new JFrame();
// The Frame uses BorderLayout by default
frame.setLayout(new BorderLayout()); 

// Create a panel for the top section
JPanel topPanel = new JPanel(); 
topPanel.setLayout(new FlowLayout()); // This panel uses FlowLayout
topPanel.add(new JLabel("Enter Data:"));
topPanel.add(new JTextField(10));

// Create a panel for the bottom buttons
JPanel buttonPanel = new JPanel(); 
buttonPanel.setLayout(new FlowLayout()); 
buttonPanel.add(doubleButton);
buttonPanel.add(resetButton);

// Nest the panels into the Frame's BorderLayout regions
frame.add(topPanel, BorderLayout.NORTH);
frame.add(buttonPanel, BorderLayout.SOUTH);
```

By mastering the hierarchy and nesting your layout managers, you can build highly professional, cleanly organized user interfaces without ever writing a single line of manual pixel-positioning code.