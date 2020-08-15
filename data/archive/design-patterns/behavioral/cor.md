# Behavioural  patterns
### Chain of Responsibilities pattern

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

#####Usage examples: 
The Chain of Responsibility pattern isn’t a frequent guest in a Java program since it’s only relevant when code operates with chains of objects.
One of the most popular use cases for the pattern is bubbling events to the parent components in GUI classes. Another notable use case is sequential access filters.

Here are some examples of the pattern in core Java libraries:

```
javax.servlet.Filter#doFilter()
java.util.logging.Logger#log()
```
Identification: The pattern is recognizable by behavioral methods of one group of objects that indirectly call the same methods in other objects, while all the objects follow the common interface.
