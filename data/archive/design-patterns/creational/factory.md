# Creational design patterns
## Factory pattern

Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

```
It provides a way to delegate the instantiation logic to child classes.
```

Usage examples: Imagine a character generator for a role playing game. The easiest option is to let computer create the character for you. But if you want to select the character details like profession, gender, hair color etc. the character generation becomes a step-by-step process that completes when all the selections are ready.

Wikipedia says

```
In class-based programming, the factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.``
```

### Programmatic Example

Imagine a button that can be shown in Windows or Html pages. Technically the button in Windows has different render process than in browsers like Chrome.
Let's for now design an interface to define a button!

~
public interface Button {
    void render();
    void onClick();
}
~
Now let's implement a button for html pages!

~
public class HtmlButton implements Button {

    public void render() {
        System.out.println("<button>Test Button</button>");
        
        // render it on Chrome engine
    }

    public void onClick() {
        System.out.println("Click! Button says - 'Hello World!'");
    }
}
~

Also we can have Windows button like below:

~
public class WindowsButton implements Button {
    JPanel panel = new JPanel();
    JFrame frame = new JFrame();
    JButton button;

    public void render() {
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        JLabel label = new JLabel("Hello World!");
        label.setOpaque(true);
        label.setBackground(new Color(235, 233, 126));
        label.setFont(new Font("Dialog", Font.BOLD, 44));
        label.setHorizontalAlignment(SwingConstants.CENTER);
        panel.setLayout(new FlowLayout(FlowLayout.CENTER));
        frame.getContentPane().add(panel);
        panel.add(label);
        onClick();
        panel.add(button);

        frame.setSize(320, 200);
        frame.setVisible(true);
        onClick();
    }

    public void onClick() {
        button = new JButton("Exit");
        button.addActionListener(e -> {
            frame.setVisible(false);
            System.exit(0);
        });
    }
}
~ 

Also we can have a dialog to put this button in.

~
public abstract class Dialog {

    public void renderWindow() {
        // ... other code ...

        Button okButton = createButton();
        okButton.render();
    }

    /**
     * Subclasses will override this method in order to create specific button
     * objects.
     */
    public abstract Button createButton();
}

~

This abstract class takes (here create) whatever the button on the page and renders them. 

We need to have ***HtmlDialog*** defined like this:

~
public class HtmlDialog extends Dialog {

    @Override
    public Button createButton() {
        return new HtmlButton();
    }
}
~

And ***WindowsDialog*** defined like this:

~
public class WindowsDialog extends Dialog {

    @Override
    public Button createButton() {
        return new WindowsButton();
    }
}
~

And finally use it like this; Try it! it opens a frame in your PC and put a button inside it.

~
public class Demo {
    private static Dialog dialog;

    public static void main(String[] args) {
        configure();
        runBusinessLogic();
    }

    /**
     * The concrete factory is usually chosen depending on configuration or
     * environment options.
     */
    static void configure() {
        String osType = System.getProperty("os.name");
        if (osType.equals("Windows 10") || osType.equals("Linux") ) {
            dialog = new WindowsDialog();
        } else {
            dialog = new HtmlDialog();
        }
    }

    /**
     * All of the client code should work with factories and products through
     * abstract interfaces. This way it does not care which factory it works
     * with and what kind of product it returns.
     */
    static void runBusinessLogic() {
        dialog.renderWindow();
    }
}

~ 

#### Real world examples
```
java.swing.Button
java.swing.JDialog
java.util.Calendar
java.util.ResourceBundle
java.text.NumberFormat
java.nio.charset.Charset
java.net.URLStreamHandlerFactory
java.util.EnumSet
javax.xml.bind.JAXBContext
```

That was all about the factory design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/factory).
Also another deign pattern examples are in [this repository](https://github.com/metao1)

To support me simply give me a star [GitHub project of this project](https://github.com/metao1/design-patterns)


