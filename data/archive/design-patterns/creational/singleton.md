# Creational  patterns
### Singleton pattern

Ensure a class only has one instance, and provide a global point of access to it.

```
Ensures that only one object of a particular class is ever created.
```

Wikipedia says

```
In software engineering, the singleton pattern is a software design pattern that 
restricts the instantiation of a class to one object. This is useful when exactly on
object is needed to coordinate actions across the system.
```

Programmatic Example

So many developers -articles- are using the below or almost equal pattern to create an instance of a singleton object. 
 
~
public final class Singleton {
    private static Singleton instance;
    public String value;

    private Singleton(String value) {
        // The following code emulates slow initialization.
        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
        this.value = value;
    }

    public static Singleton getInstance(String value) {
        if (instance == null) {
            instance = new Singleton(value);
        }
        return instance;
    }
}
~

And one can use it this way:

~
    Singleton singleton = Singleton.getInstance("FOO");
~

While this is a right singleton pattern, this is not thread-safe at all!  

As we mentioned earlier a singleton object should ensure that only one object of a particular class is ever created.
When you run the above code using multiple threads, it can be seen that there might be several threads 
enters into the critical area, where the instance of object is generating.


~
if (instance == null) {
        // here there might be more than one thread to come
    instance = new Singleton(value);
}
~

So if the first thread generates the instance the second one runs after can override the object!
Then this is not a good approach for multi-threaded environment.

To solve this problem we apply ***synchronized*** for the ***getInstance*** method! Although this method can be slow
for heavy constructors, tt blocks any thread until an object is creating so it is thread-safe approach. 
(To resolve this problem of latency for those heavy constructors search in this website for lazy-loading pattern)

This ensures that next threads wait until the first thread initiates object. They use that object and won't override. 
We change it to the following easily:

~
public final class Singleton {
    private static Singleton instance;
    public String value;

    private Singleton(String value) {
        // The following code emulates slow initialization.
        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
        this.value = value;
    }

    public static synchronized Singleton getInstance(String value) {
        if (instance == null) {
            instance = new Singleton(value);
        }
        return instance;
    }
}
~


That was all about the singleton design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/singleton).
Also another deign pattern examples are in [this repository](https://github.com/metao1)

To support me simply give me a star [GitHub project of this project](https://github.com/metao1/design-patterns)

