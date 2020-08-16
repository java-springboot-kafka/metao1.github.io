# Behavioural design patterns
## Chain of Responsibilities pattern

Chain of Responsibility is a behavioral design pattern that lets you pass requests along a chain of handlers.
Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.


Wikipedia says:

```
The chain-of-responsibility pattern is structurally nearly identical to the decorator pattern, the difference being that for the decorator,
all classes handle the request, while for the chain of responsibility, exactly one of the classes in the chain handles the request. 
This is a strict definition of the Responsibility concept in the GoF book. 
However, many implementations (such as loggers below, or UI event handling, or servlet filters in Java, etc) allow several
elements in the chain to take responsibility.
```

Imagine that you’re working on an online ordering system. You want to restrict access to the system so only authenticated users can create orders. 
Also, users who have administrative permissions must have full access to all orders.

After a bit of planning, you realized that these checks must be performed sequentiall
y. The application can attempt to authenticate a user to the system whenever it receives a request that contains the user’s credentials. 
However, if those credentials aren’t correct and authentication fails, there’s no reason to proceed with any other checks.

During the next few months, you implemented several more of those sequential checks.

1. One of your colleagues suggested that it’s unsafe to pass raw data straight to the ordering system. So you added an extra validation step to sanitize the data in a request.

2. Later, somebody noticed that the system is vulnerable to brute force password cracking. To negate this, you promptly added a check that filters repeated failed requests coming from the same IP address.

3. Someone else suggested that you could speed up the system by returning cached results on repeated requests containing the same data. Hence, you added another check which lets the request pass through to the system only if there’s no suitable cached response.

##### Usage examples: 
The Chain of Responsibility pattern isn’t a frequent guest in a Java program since it’s only relevant when code operates with chains of objects.
One of the most popular use cases for the pattern is bubbling events to the parent components in GUI classes. Another notable use case is sequential access filters.

Here are some examples of the pattern in core Java libraries:

```
javax.servlet.Filter#doFilter()
java.util.logging.Logger#log()
```
Identification: The pattern is recognizable by behavioral methods of one group of objects that indirectly call the same methods in other objects, while all the objects follow the common interface.

### Filtering access
This example shows how a request containing user data passes a sequential chain of handlers that perform various things such as authentication, authorization, and validation.

This example is a bit different from the canonical version of the pattern given by various authors. 
Most of the pattern examples are built on the notion of looking for the right handler, launching it and exiting the chain after that. 
But here we execute every handler until there’s one that can’t handle a request. Be aware that this still is the Chain of Responsibility pattern, 
even though the flow is a bit different.


~
/* Base middleware class.*/
public abstract class Middleware {
    private Middleware next;

    /* Builds chains of middleware objects.*/
    public Middleware linkWith(Middleware next) {
        this.next = next;
        return next;
    }

    
    /* Subclasses will implement this method with concrete checks.*/
    public abstract boolean check(String email, String password);

    /* Runs check on the next object in chain or ends traversing if we're in */
    /* last object in chain.*/
    protected boolean checkNext(String email, String password) {
        if (next == null) {
            return true;
        }
        return next.check(email, password);
    }
}
~

Check request amount limit is done with a simple class like below.

***There is a dedicated design pattern that I've covered in this website.
Make sure to search for throttling design pattern, it is a better approach dedicated for throttling only*** 

~
public class ThrottlingMiddleware extends Middleware {
    private int requestPerMinute;
    private int request;
    private long currentTime;

    public ThrottlingMiddleware(int requestPerMinute) {
        this.requestPerMinute = requestPerMinute;
        this.currentTime = System.currentTimeMillis();
    }

    /* Please, not that checkNext() call can be inserted both in the beginning of this method and in the end.. */
    public boolean check(String email, String password) {
        if (System.currentTimeMillis() > currentTime + 60_000) {
            request = 0;
            currentTime = System.currentTimeMillis();
        }

        request++;
        
        if (request > requestPerMinute) {
            System.out.println("Request limit exceeded!");
            Thread.currentThread().stop();
        }
        return checkNext(email, password);
    }
}
~

Check user’s credentials:

~
/* ConcreteHandler. Checks whether a user with the given credentials exists.*/
public class UserExistsMiddleware extends Middleware {
    private Server server;

    public UserExistsMiddleware(Server server) {
        this.server = server;
    }

    public boolean check(String email, String password) {
        if (!server.hasEmail(email)) {
            System.out.println("This email is not registered!");
            return false;
        }
        if (!server.isValidPassword(email, password)) {
            System.out.println("Wrong password!");
            return false;
        }
        return checkNext(email, password);
    }
}
~

Check user’s role

~
/* ConcreteHandler. Checks a user's role.*/
public class RoleCheckMiddleware extends Middleware {
    public boolean check(String email, String password) {
        if (email.equals("admin@example.com")) {
            System.out.println("Hello, admin!");
            return true;
        }
        System.out.println("Hello, user!");
        return checkNext(email, password);
    }
}
~

Authorization target

~
import java.util.HashMap;
import java.util.Map;

/** Server class.*/
public class Server {
    private Map<String, String> users = new HashMap<>();
    private Middleware middleware;

    /**Client passes a chain of object to server. This improves flexibility and makes testing the server class easier.*/
    public void setMiddleware(Middleware middleware) {
        this.middleware = middleware;
    }

    /**Server gets email and password from client and sends the authorization request to the chain.*/
    public boolean logIn(String email, String password) {
        if (middleware.check(email, password)) {
            System.out.println("Authorization have been successful!");

            // Do something useful here for authorized users.

            return true;
        }
        return false;
    }

    public void register(String email, String password) {
        users.put(email, password);
    }

    public boolean hasEmail(String email) {
        return users.containsKey(email);
    }

    public boolean isValidPassword(String email, String password) {
        return users.get(email).equals(password);
    }
}
~

And the usage would be like this:

~
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

/* Demo class. Everything comes together here.*/
public class Demo {
    private static BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
    private static Server server;

    private static void init() {
        server = new Server();
        server.register("admin@example.com", "admin_pass");
        server.register("user@example.com", "user_pass");

        // All checks are linked. Client can build various chains using the same
        // components.
        Middleware middleware = new ThrottlingMiddleware(2);
        middleware.linkWith(new UserExistsMiddleware(server))
                .linkWith(new RoleCheckMiddleware());

        // Server gets a chain from client code.
        server.setMiddleware(middleware);
    }

    public static void main(String[] args) throws IOException {
        init();

        boolean success;
        do {
            System.out.print("Enter email: ");
            String email = reader.readLine();
            System.out.print("Input password: ");
            String password = reader.readLine();
            success = server.logIn(email, password);
        } while (!success);
    }
}
~
