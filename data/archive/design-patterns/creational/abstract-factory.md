# Creational design patterns
## Abstract Factory Pattern

A factory of factories; a factory that groups the individual but related/dependent factories together without specifying their concrete classes.

``
Allows you to create different flavors of an object while avoiding constructor pollution. Useful when there could be several flavors of an object. Or when there are a lot of steps involved in creation of an object.
``

Usage examples: Imagine a character generator for a role playing game. The easiest option is to let computer create the character for you. But if you want to select the character details like profession, gender, hair color etc. the character generation becomes a step-by-step process that completes when all the selections are ready.

Wikipedia says

```
The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes
```

In the Abstract Factory pattern, we get rid of if-else block and have a factory class for each sub-class. Then an Abstract Factory class that will return the sub-class based on the input factory class. At first, it seems confusing but once you see the implementation, it’s really easy to grasp and understand the minor difference between Factory and Abstract Factory pattern.

Like our factory pattern post, we will use the same superclass and sub-classes.

####Abstract Factory Design Pattern Super Class and Subclasses

Example:

~
public abstract class Computer {
     
    public abstract String getRAM();
    public abstract String getHDD();
    public abstract String getCPU();
     
    @Override
    public String toString(){
        return "RAM= "+this.getRAM()+", HDD="+this.getHDD()+", CPU="+this.getCPU();
    }
}
~

~
public class PC extends Computer {
 
    private String ram;
    private String hdd;
    private String cpu;
     
    public PC(String ram, String hdd, String cpu){
        this.ram=ram;
        this.hdd=hdd;
        this.cpu=cpu;
    }
    @Override
    public String getRAM() {
        return this.ram;
    }
 
    @Override
    public String getHDD() {
        return this.hdd;
    }
 
    @Override
    public String getCPU() {
        return this.cpu;
    }
 
}
~


~
public class Server extends Computer {
 
    private String ram;
    private String hdd;
    private String cpu;
     
    public Server(String ram, String hdd, String cpu){
        this.ram=ram;
        this.hdd=hdd;
        this.cpu=cpu;
    }
    @Override
    public String getRAM() {
        return this.ram;
    }
 
    @Override
    public String getHDD() {
        return this.hdd;
    }
 
    @Override
    public String getCPU() {
        return this.cpu;
    }
 
}
~

~
public interface ComputerAbstractFactory {

	public Computer createComputer();

}
~

~
public class PCFactory implements ComputerAbstractFactory {

	private String ram;
	private String hdd;
	private String cpu;
	
	public PCFactory(String ram, String hdd, String cpu){
		this.ram=ram;
		this.hdd=hdd;
		this.cpu=cpu;
	}
	@Override
	public Computer createComputer() {
		return new PC(ram,hdd,cpu);
	}

}
~

Similarly we will have a factory class for Server subclass.

~
public class ServerFactory implements ComputerAbstractFactory {

	private String ram;
	private String hdd;
	private String cpu;
	
	public ServerFactory(String ram, String hdd, String cpu){
		this.ram=ram;
		this.hdd=hdd;
		this.cpu=cpu;
	}
	
	@Override
	public Computer createComputer() {
		return new Server(ram,hdd,cpu);
	}

}
~

Now we will create a consumer class that will provide the entry point for the client classes to create sub-classes.

~
public class ComputerFactory {
	public static Computer getComputer(ComputerAbstractFactory factory){
		return factory.createComputer();
	}
}
~


Notice that its a simple class and ***getComputer*** method is accepting ***ComputerAbstractFactory*** argument and returning ***Computer*** object. 
At this point the implementation must be getting clear.

Let’s write a simple test method and see how to use the abstract factory to get the instance of sub-classes.

~
public class TestDesignPatterns {

	public static void main(String[] args) {
		Computer pc = ComputerFactory.getComputer(new PCFactory("2 GB","500 GB","2.4 GHz"));
        Computer server = ComputerFactory.getComputer(new ServerFactory("16 GB","1 TB","2.9 GHz"));
        System.out.println("AbstractFactory PC Config::"+pc);
        System.out.println("AbstractFactory Server Config::"+server);
	}
}
~

#### Real world examples
```
java.lang.StringBuilder
java.nio.ByteBuffer as well as similar buffers such as FloatBuffer, IntBuffer and so on.
java.lang.StringBuffer
All implementations of java.lang.Appendable
Apache Camel builders
Apache Commons Option.Builder
```

That was all about the builder design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/builder).
Also another deign pattern examples are in [this repository](https://github.com/metao1)

To support me simply give me a star [GitHub project of this project](https://github.com/metao1/design-patterns)


