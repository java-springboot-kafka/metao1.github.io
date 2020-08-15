# Creational  patterns
### Prototype pattern

Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype.

First it should be noted that Prototype pattern is not used to gain performance benefits. It's only used for creating new objects from prototype instance.

``
Create object based on an existing object through cloning.
``

Usage examples:  In short, it allows you to create a copy of an existing object and modify it to your needs, instead of going through the trouble of creating an object from scratch and setting it up.   

Wikipedia says

```
The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects.
```

###Programmatic Example

In Java, it can be easily done by implementing Cloneable and overriding clone from Object

~
class Sheep implements Cloneable {
  private String name;
  public Sheep(String name) { this.name = name; }
  public void setName(String name) { this.name = name; }
  public String getName() { return name; }
  @Override
  public Sheep clone() {
    try {
      return (Sheep)super.clone();
    } catch(CloneNotSuportedException) {
      throw new InternalError();
    }
  }
}

~

Then it can be cloned like below

~
var original = new Sheep("Jolly");
System.out.println(original.getName()); // Jolly

// Clone and modify what is required
var cloned = original.clone();
cloned.setName("Dolly");
System.out.println(cloned.getName()); // Dolly

~

#####Real world examples
```
java.lang.Object#clone()
```

That was all about the prototype design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/prototype).
Also another deign pattern examples are in [this repository](https://github.com/metao1)

To support me simply give me a star [GitHub project of this project](https://github.com/metao1/design-patterns)


